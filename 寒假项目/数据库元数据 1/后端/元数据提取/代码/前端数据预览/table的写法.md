没问题！这是**最严谨、最安全**的做法。

我们利用 `DatabaseMetaData` 先去问数据库“这张表是否存在”，确认存在后，再使用 **MSSQL 专用语法** (`TOP 100` + `[]`) 来查询数据。

请将 `MetadataService.java` 中的 `getTableData` 方法替换为以下代码：

Java
[[获取列名]]


```java
    /**
     * 获取表数据预览 (MSSQL 专用 + 元数据校验版)
     * 1. 先校验表是否存在 (防止 SQL 注入)
     * 2. 再查询前 100 条数据
     */
    public Map<String, Object> getTableData(String token, String schema, String tableName) throws Exception {
        Connection conn = connectionCathe.getConnection(token);
        if (conn == null) {
            throw new RuntimeException("连接已断开，请重试！");
        }

        // --- 🛡️ 步骤1：元数据验明正身 (白名单校验) ---
        // 直接问数据库：有没有这张表？
        DatabaseMetaData meta = conn.getMetaData();
        
        // getTables 参数说明: catalog(库名), schema(模式), tableName(表名), types(类型)
        // 对于 MSSQL，catalog 传 null 即可（因为它使用当前连接的库），schema 必须传
        ResultSet tableCheckRs = meta.getTables(null, schema, tableName, new String[]{"TABLE", "VIEW"});
        
        boolean exists = tableCheckRs.next();
        tableCheckRs.close();
        
        if (!exists) {
            // 如果数据库里查不到这张表，直接抛异常，后续 SQL 根本不会执行
            throw new IllegalArgumentException("表 [" + tableName + "] 不存在或无权访问！");
        }

        // --- 🔧 步骤2：拼接 MSSQL 专用 SQL ---
        // 既然上面校验通过了，说明 tableName 是安全的，可以放心拼接
        StringBuilder sqlBuilder = new StringBuilder();
        
        // MSSQL 语法：SELECT TOP 100 * ...
        sqlBuilder.append("SELECT TOP 100 * FROM ");
        
        // 加上中括号 [] 是 MSSQL 的标准防冲突写法
        if (schema != null && !schema.isEmpty()) {
            sqlBuilder.append("[").append(schema).append("].");
        }
        sqlBuilder.append("[").append(tableName).append("]");

        String sql = sqlBuilder.toString();

        // --- 🚀 步骤3：执行查询并获取结果 ---
        Statement stmt = conn.createStatement();
        ResultSet rs = stmt.executeQuery(sql);

        Map<String, Object> result = new HashMap<>();
        List<String> columnNames = new ArrayList<>();
        List<Map<String, Object>> rows = new ArrayList<>();

        // 3.1 获取列名 (ResultSetMetaData)
        ResultSetMetaData rsMeta = rs.getMetaData();
        int columnCount = rsMeta.getColumnCount();
        for (int i = 1; i <= columnCount; i++) {
            columnNames.add(rsMeta.getColumnLabel(i));
        }

        // 3.2 获取行数据
        while (rs.next()) {
            Map<String, Object> row = new LinkedHashMap<>(); // 保持顺序
            for (String colName : columnNames) {
                Object val = rs.getObject(colName);
                // 特殊处理：MSSQL 的一些特殊类型可能需要转 String，防止前端 JSON 解析报错
                // 这里暂时直接 put，如果以后遇到报错（比如二进制流），可以在这里加 if 判断
                row.put(colName, val);
            }
            rows.add(row);
        }

        // 3.3 组装返回
        result.put("columns", columnNames);
        result.put("rows", rows);

        // 关闭资源 (Connection 不关，因为它在缓存里)
        rs.close();
        stmt.close();

        return result;
    }
```

### 💡 核心逻辑解析

1. **安全性 (Step 1)**：
    
    - 代码根本不去尝试正则匹配，而是直接调用 `conn.getMetaData().getTables(...)`。
        
    - 如果黑客传了个 `t_student; DROP TABLE`，`getTables` 会去数据库里找名字叫 `"t_student; DROP TABLE"` 的表。显然找不到，于是 `exists` 为 `false`，直接报错拦截。**绝对安全。**
        
2. **MSSQL 语法 (Step 2)**：
    
    - 使用了 `SELECT TOP 100`（替代了 MySQL 的 `LIMIT`）。
        
    - 使用了 `[schema].[table]` 格式（这是 SQL Server 的官方引用符，防止表名和关键字冲突）。
        
3. **结果封装 (Step 3)**：
    
    - 依然返回 `columns` 和 `rows`，完美适配我们之前改好的前端逻辑（即使是空表也能显示表头）。
        

快把这段代码贴进去测试一下吧！如果你的前端已经改好了，现在双击表名，应该就能看到数据了。