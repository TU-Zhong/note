`Properties` 是 Java 中一个非常经典且常用的类（位于 `java.util` 包下）。

简单来说，你可以把它理解为一个**专门用来存“配置信息”的字典（Map）**。

### 1. 它是什么？

它的核心特性非常简单：

- **结构**：它是 **Key-Value（键值对）** 结构。
    
- **限制**：它的 Key（键）和 Value（值）**强制要求必须都是字符串 (String)**。
    

通俗比喻：

它就像是一张**“设置清单”**。

- `Key`: 设置项的名字（比如 "user", "fontSize", "language"）。
    
- `Value`: 设置项的具体内容（比如 "root", "12px", "zh_CN"）。
    

---

### 2. 在你的代码里是干嘛用的？

在 JDBC 编程中，`Properties` 扮演了一个**“万能参数包”**的角色。

Java

```
Properties props = new Properties();
props.setProperty("user", username);
props.setProperty("password", password);
// 还可以加别的：
props.setProperty("encrypt", "true");
props.setProperty("loginTimeout", "30");

// 把这个包递给驱动
driver.connect(url, props);
```

为什么要用它，而不是直接传参数？

假设没有 Properties，driver.connect 方法可能得长这样：

connect(url, user, password, encryption, timeout, certificate, ...)

如果有 20 个配置项，这个方法就没法写了。

**使用 `Properties` 的好处：**

- **灵活性**：驱动程序不需要定义死板的参数列表。你把所有需要的配置（账号、密码、加密开关、超时时间等）都塞进这个 `props` 对象里，打包递给驱动程序，驱动程序自己去里面翻它需要的东西。
    

---

### 3. 一般怎么用？（通用场景）

除了 JDBC，`Properties` 在 Java 开发中最常见的用途是**读写配置文件**。

你肯定见过很多 `.properties` 结尾的文件（比如 Spring Boot 的 `application.properties`），它们就是为此而生的。

#### 场景一：读取配置文件

假设你有一个文件 `config.properties`：

Properties

```
db.url=jdbc:mysql://localhost:3306/test
db.user=root
app.version=1.0.0
```

Java 代码读取它：

Java

```
Properties props = new Properties();
// 加载文件
props.load(new FileInputStream("config.properties"));

// 获取值
String user = props.getProperty("db.user"); // 结果是 "root"
String ver = props.getProperty("app.version"); // 结果是 "1.0.0"
```

#### 场景二：写入配置文件

Java

```
Properties props = new Properties();
props.setProperty("lastRunTime", "2026-01-20");
props.setProperty("status", "success");

// 保存到文件
props.store(new FileOutputStream("status.properties"), "这是注释");
```

---

### 总结

在你的毕业设计代码中：

Properties 就是一个**“参数背包”。你把连接数据库需要的账号、密码、SSL配置等零碎信息都塞（setProperty）进去，然后把整个背包扔**给驱动程序，让它看着办。