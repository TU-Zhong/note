太棒了！从数据层面的 CRUD（增删改查）正式跨越到**结构层面的 DDL（数据定义语言）**，这标志着你的项目正在从一个“高级数据浏览器”蜕变为真正的“全功能数据库 IDE”。

根据你提供的树形结构截图，结合咱们之前定下的“商业级严谨架构”基调，我为你起草了一份**《数据库表结构变更 (DDL) 功能设计与开发文档》**。

这份文档不仅包含了前后端的交互逻辑，更提前帮你把做底层 IDE 最容易踩的几个“天坑”给标出来了。

---

# 📝 数据库表结构变更 (DDL) 功能设计文档

## 一、 需求概述

在现有的左侧数据库对象树（Tree）中，允许用户对指定的表进行结构级别的修改。

- **核心功能**：支持新增列（Column）、新增键/约束（Key）、新增索引（Index）。
    
- **技术难点**：DDL 语句不支持 JDBC 的 `?` 占位符防注入，需要自研安全拼接方案；需处理存量数据与新约束之间的冲突（如 NOT NULL 冲突）。
    

## 二、 前端交互设计 (UI/UX)

参照 Navicat 和 DataGrip 的标准体验，建议采用**“右键菜单 + 弹窗/抽屉”**的交互模式。

1. **触发方式**：
    
    - 用户在左侧树形菜单中，右键点击 `columns` 文件夹节点 -> 弹出菜单选择 **“新建列 (Add Column)”**。
        
    - 右键点击 `indexes` 文件夹节点 -> 选择 **“新建索引 (Add Index)”**。
        
2. **表单设计 (Dialog/Drawer)**：
    
    - **新建列表单**：包含 `列名 (Name)`、`数据类型 (Type, 下拉选)`、`长度 (Length)`、`允许为空 (Nullable, Checkbox)`、`默认值 (Default Value)`。
        
    - **新建索引表单**：包含 `索引名称 (Name)`、`包含的列 (Columns, 多选/穿梭框)`、`是否唯一 (Unique, Checkbox)`。
        
3. **状态更新**：
    
    - 接口调用成功后，前端触发左侧树节点的局部刷新（重新拉取该表的 columns/indexes 元数据），并展开该文件夹。
        

## 三、 前端数据结构与 API 设计

前端收集好表单数据后，统一封装成标准 JSON 发送给后端。

### 1. 新建列 (Add Column) API

- **POST** `/api/metadata/table/column/add`
    
- **Payload (JSON)**:
    

JSON

```
{
  "tableName": "Student",
  "schema": "dbo",
  "column": {
    "name": "phone_number",
    "type": "varchar",
    "length": "20",
    "isNullable": true,
    "defaultValue": ""
  }
}
```

### 2. 新建索引 (Add Index) API

- **POST** `/api/metadata/table/index/add`
    
- **Payload (JSON)**:
    

JSON

```
{
  "tableName": "Student",
  "schema": "dbo",
  "index": {
    "name": "idx_student_phone",
    "isUnique": false,
    "columns": ["phone_number"] 
  }
}
```

## 四、 后端架构设计 (JDBC 核心逻辑)

由于 DDL（Data Definition Language）不能像 DML（增删改）那样使用 `PreparedStatement` 的 `?` 占位符，我们需要通过 `Statement.execute()` 来执行动态拼接的 SQL。

### 1. 新增列的 SQL 生成引擎 (SQL Server 方言)

后端接收到数据后，进行字符串拼接：

Java

```
// 伪代码示例
StringBuilder sql = new StringBuilder("ALTER TABLE ");
sql.append(quote(schema)).append(".").append(quote(tableName)).append(" ADD ");
sql.append(quote(column.getName())).append(" ").append(column.getType());

// 处理长度 varchar(20)
if (column.getLength() != null && !column.getLength().isEmpty()) {
    sql.append("(").append(column.getLength()).append(")");
}

// 处理是否为空
if (column.isNullable()) {
    sql.append(" NULL");
} else {
    sql.append(" NOT NULL");
    // ⚠️ 商业级防坑：如果已有数据且设置了 NOT NULL，必须给默认值，否则报错！
    if (column.getDefaultValue() != null && !column.getDefaultValue().isEmpty()) {
         sql.append(" DEFAULT '").append(column.getDefaultValue()).append("'");
    }
}

// 执行 DDL
try (Statement stmt = conn.createStatement()) {
    stmt.execute(sql.toString());
}
```

### 2. 新增索引的 SQL 生成引擎

Java

```
// 伪代码示例
StringBuilder sql = new StringBuilder("CREATE ");
if (index.isUnique()) {
    sql.append("UNIQUE ");
}
sql.append("INDEX ").append(quote(index.getName()))
   .append(" ON ").append(quote(schema)).append(".").append(quote(tableName))
   .append(" (");

// 拼接多列: [col1], [col2]
StringJoiner columnsJoiner = new StringJoiner(", ");
for (String col : index.getColumns()) {
    columnsJoiner.add(quote(col));
}
sql.append(columnsJoiner.toString()).append(")");

// 执行 DDL
try (Statement stmt = conn.createStatement()) {
    stmt.execute(sql.toString());
}
```

## 五、 🚨 商业级 IDE 的“三大防坑指南”（面试必杀技）

做 DDL 操作，最容易把系统搞崩或者把数据库搞出安全漏洞，这三个坑一定要防住：

### 坑一：DDL 的 SQL 注入危机

因为不能用 `?`，如果有黑客在前端的“列名”输入框里填入 `age INT; DROP TABLE Student; --`，你的 `StringBuilder` 会直接把表给删了！

**降维方案（标识符安全转义）**：

- 必须在后端写一个 `quote()` 工具方法。
    
- 对于 SQL Server，用方括号包裹所有的表名和列名，并替换内部的方括号。例如：`[表名]`。
    
- 对于 MySQL，用反引号包裹。
    
- 永远不要信任前端传来的名称，纯粹的拼接是极其危险的！
    

### 坑二：存量数据与 NOT NULL 的物理冲突

如果你对一个**已经有 1000 行数据的表**，执行 `ALTER TABLE Student ADD new_col INT NOT NULL`。

SQL Server 会直接抛出异常，因为它不知道那 1000 行老数据的 `new_col` 该填什么。

**降维方案**：

- 在前端的“新建列”表单里加个联动校验：如果用户勾选了“不为空 (NOT NULL)”，则必须要求用户填写“默认值 (Default Value)”。
    

### 坑三：索引创建引发的“全表锁”

如果在生产环境中，对一张几千万级别的大表创建索引，普通的 `CREATE INDEX` 会锁住整张表，导致线上业务瞬间瘫痪。

**进阶亮点（可选）**：

- 可以在前端加上一个高级选项 Switch：`在线创建 (Online)`。
    
- 如果勾选，后端 SQL 拼接为 `CREATE INDEX ... WITH (ONLINE = ON)`（需评估 SQL Server 版本支持情况），这在面试中是非常能体现你 DBA 实战经验的亮点。
    

---

**下一步行动：**

我建议咱们先从**“新建列 (Add Column)”**开始做起。你可以先在 Vue 项目里，把左侧树的右键菜单（Context Menu）或者弹窗给画出来。

如果你不知道前端怎么优雅地实现树节点的右键菜单，或者不知道怎么写后端的安全 `quote` 防注入函数，随时告诉我！我们一起搞定它！