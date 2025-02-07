在 Spring Boot 中，`@Value` 注解用于将配置文件中的值注入到 Spring 管理的 Bean 中。它支持从 `application.properties` 或 `application.yml` 等配置文件中读取值，也可以直接注入静态值或表达式。

---

### 常用方法

#### 1. 注入配置文件中的值
假设 `application.properties` 中有如下配置：
```properties
app.name=MyApplication
app.version=1.0.0
app.description=This is a Spring Boot application
```

在代码中使用 `@Value` 注入：
```java
@Component
public class AppConfig {

    @Value("${app.name}")
    private String appName;

    @Value("${app.version}")
    private String appVersion;

    @Value("${app.description}")
    private String appDescription;

    // Getter 方法
}
```

---

#### 2. 注入默认值
如果配置文件中没有对应的值，可以设置默认值：
```java
@Component
public class AppConfig {

    @Value("${app.author:Unknown}")
    private String appAuthor; // 如果 app.author 未配置，默认值为 "Unknown"
}
```

---

#### 3. 注入系统属性或环境变量
`@Value` 也可以注入系统属性或环境变量：
```java
@Component
public class AppConfig {

    @Value("${java.home}")
    private String javaHome; // 注入 Java 安装路径

    @Value("${user.name}")
    private String userName; // 注入当前用户名称
}
```

---

#### 4. 注入静态值
可以直接注入静态值，而不需要从配置文件中读取：
```java
@Component
public class AppConfig {

    @Value("Hello, World!")
    private String greeting; // 直接注入静态值
}
```

---

#### 5. 注入表达式
`@Value` 支持 SpEL（Spring Expression Language），可以注入动态值或计算结果：
```java
@Component
public class AppConfig {

    @Value("#{T(java.lang.Math).random() * 100}")
    private double randomNumber; // 注入一个随机数（0到100之间）
}
```

---

#### 6. 注入数组或列表
可以注入配置文件中的数组或列表：
假设 `application.properties` 中有如下配置：
```properties
app.ports=8080,8081,8082
```

在代码中使用 `@Value` 注入：
```java
@Component
public class AppConfig {

    @Value("${app.ports}")
    private int[] ports; // 注入数组 [8080, 8081, 8082]

    @Value("#{'${app.ports}'.split(',')}")
    private List<String> portList; // 注入列表 ["8080", "8081", "8082"]
}
```
解释一下为什么#{'{app.ports}'.split(',')}要在前面加引号以及后面使用split函数
[[SpEL（Spring Expression Language）]]
[[value里面的一些参数问题]]
---

#### 7. 注入 Map
可以注入配置文件中的 Map：
假设 `application.properties` 中有如下配置：
```properties
app.config.key1=value1
app.config.key2=value2
```

在代码中使用 `@Value` 注入：
```java
@Component
public class AppConfig {

    @Value("#{${app.config}}")
    private Map<String, String> configMap; // 注入 Map {key1=value1, key2=value2}
}
```

---

### 总结
`@Value` 注解的常用方法包括：
1. 从配置文件中注入值。
2. 设置默认值。
3. 注入系统属性或环境变量。
4. 注入静态值。
5. 使用 SpEL 注入动态值或计算结果。
6. 注入数组或列表。
7. 注入 Map。

通过灵活使用 `@Value`，可以方便地将外部配置或动态值注入到 Spring Boot 应用程序中。