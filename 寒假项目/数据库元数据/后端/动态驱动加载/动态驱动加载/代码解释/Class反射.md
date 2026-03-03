这种写法在 Java 中叫做 **“无界通配符泛型” (Unbounded Wildcard Generic)**。

听起来很吓人？其实它的含义非常简单，用大白话翻译过来就是：

> **“这是一个 Class 对象，但它具体代表哪个类？我现在还不知道，反正是什么都行。”**

我们把它拆开来看：

### 1. `Class` 是什么？

在 Java 里，`Class` 是一个特殊的类。它的每一个实例都代表**“一种类型”**（或者说一张图纸）。

- 比如 `String` 有它对应的 `Class` 对象。
    
- 比如 `Student` 有它对应的 `Class` 对象。
    

### 2. `< >` 泛型是什么？

通常我们用泛型来限制类型，为了安全。

- `Class<String>`：意思是“这是专属于 String 类的图纸”。如果你试图把 `Integer` 的图纸放进去，编译器会报错。
    
- `Class<User>`：意思是“这是专属于 User 类的图纸”。
    

### 3. `?` (问号) 是什么？

? 在 Java 泛型里代表 “任意类型” 或 “未知类型”。

所以 Class<?> 的意思就是：“这是一个类的图纸，但它是谁的图纸？可能是 String，可能是 Driver，也可能是 Dog。我不在乎，我不做限制。”

---

### 为什么要在这里用 `<?>`？

看看你的代码场景：

Java

```
// 你的入参 driverClassName 是一个字符串，比如 "com.microsoft...SQLServerDriver"
// 但它也可能是 "com.mysql...Driver"
// 甚至可能是 "java.lang.String" (如果用户乱填的话)
String driverClassName = "com.microsoft.sqlserver.jdbc.SQLServerDriver";

Class<?> driverClass = ucl.loadClass(driverClassName);
```

原因 1：编译器无法预测

因为 driverClassName 是一个变量（字符串）。在代码写完编译的时候，Java 编译器根本不知道这个字符串运行起来会变成什么。

- 编译器：_“老兄，你让我加载一个类，但只有上帝知道这行代码跑起来的时候，那个字符串是个啥。既然我确定不了它是 `Class<Driver>` 还是 `Class<String>`，那我就只能把它定义为 `Class<?>`（未知的 Class）。”_
    

原因 2：为了通用性

你的这个方法 loadClass 是通用的。

- 今天它加载 SQL Server 驱动。
    
- 明天它加载 MySQL 驱动。
    
- 后天它可能加载 Oracle 驱动。
    
    这三个驱动是完全不同的类。唯一能兼容它们的写法，就是“任意类” (Class<?>)。
    

---

### 对比一下就懂了

|**写法**|**含义**|**你的场景能用吗？**|
|---|---|---|
|`Class<String> clazz`|这个变量只能存 String 类的图纸|**不能**。你存的是数据库驱动，不是 String。|
|`Class<SQLServerDriver> clazz`|只能存 SQL Server 的图纸|**不能**。如果你换成 MySQL 驱动就会报错，不灵活。|
|`Class clazz` (不带尖括号)|原始写法 (Java 5 以前)|**能用，但不好**。会有黄色警告，不规范。|
|**`Class<?> clazz`**|**存什么类的图纸都行**|**完美**。既规范，又支持任意驱动。|

### 总结

`Class<?>` 就是 Java 里一种**“最宽容”**的写法。它在告诉编译器：

> **“我要加载一个类进来，虽然我现在还不知道它的具体名字，但请先帮我把它当成一个通用的 Class 对象存起来，后面我会用反射去处理它。”**