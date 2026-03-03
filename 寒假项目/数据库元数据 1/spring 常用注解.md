这是一个非常实用的请求。Spring Boot 的强大之处很大程度上依赖于其丰富的注解（Annotations），这些注解极大地简化了配置和开发流程。

为了方便你阅读和记忆，我将这 30 个常用注解按**功能模块**进行了分类整理。

---

### 一、 核心启动与配置类 (Core & Bootstrapping)

这是 Spring Boot 应用程序的基石。

1. **`@SpringBootApplication`**
    
    - **用法：** 放在主类（Main Class）上。
        
    - **作用：** 这是一个组合注解，包含了 `@Configuration`、`@EnableAutoConfiguration` 和 `@ComponentScan`。它是项目的启动入口，开启自动配置和组件扫描。
        
2. **`@Configuration`**
    
    - **用法：** 放在类上。
        
    - **作用：** 声明该类是一个配置类，等同于 XML 配置文件。通常与 `@Bean` 配合使用。
        
3. **`@Bean`**
    
    - **用法：** 放在方法上。
        
    - **作用：** 将方法返回的对象注册到 Spring IoC 容器中，方法名默认为 Bean 的 ID。
        
4. **`@EnableAutoConfiguration`**
    
    - **用法：** 放在类上（通常由 `@SpringBootApplication` 包含）。
        
    - **作用：** 告诉 Spring Boot 根据添加的 jar 依赖自动配置 Spring 应用。
        
5. **`@ComponentScan`**
    
    - **用法：** 放在类上。
        
    - **作用：** 指定 Spring 扫描组件（如 `@Service`, `@Component` 等）的包路径。默认扫描主类所在的包及其子包。
        

---

### 二、 组件注册 (Component Registration)

用于将普通的 Java 类标识为 Spring 容器管理的 Bean。

6. **`@Component`**
    
    - **用法：** 放在类上。
        
    - **作用：** 通用的组件注解，表示该类由 Spring 管理。如果不知道归类到哪一层，可以用它。
        
7. **`@Service`**
    
    - **用法：** 放在业务逻辑层（Service Layer）类上。
        
    - **作用：** 特指业务逻辑组件，功能上等同于 `@Component`，但语义更清晰。
        
8. **`@Repository`**
    
    - **用法：** 放在数据访问层（DAO Layer）类上。
        
    - **作用：** 标识数据访问组件。它还能将数据库底层的异常转换为 Spring 的 `DataAccessException`。
        
9. **`@Controller`**
    
    - **用法：** 放在 Web 层类上。
        
    - **作用：** 标识该类为 Spring MVC 的控制器，通常用于返回视图（JSP/Thymeleaf 页面）。
        
10. **`@RestController`**
    
    - **用法：** 放在 Web 层类上。
        
    - **作用：** 它是 `@Controller` 和 `@ResponseBody` 的组合。用于返回 JSON 或 XML 数据，而不是页面，是开发 RESTful API 的首选。
        
11. **`@Scope`**
    
    - **用法：** 放在类上。
        
    - **作用：** 定义 Bean 的作用域（如 `singleton` 单例, `prototype` 多例, `request`, `session`）。
        

---

### 三、 依赖注入 (Dependency Injection)

用于在 Bean 之间建立依赖关系。

12. **`@Autowired`**
    
    - **用法：** 放在字段、构造器或 Setter 方法上。
        
    - **作用：** Spring 提供的注解，默认按**类型**（byType）自动注入依赖。
        
13. **`@Qualifier`**
    
    - **用法：** 与 `@Autowired` 配合使用。
        
    - **作用：** 当同一个接口有多个实现类时，用于指定注入哪一个具体的 Bean（按名称）。
        
14. **`@Resource`**
    
    - **用法：** 放在字段上。
        
    - **作用：** Java 标准注解（JSR-250）。默认按**名称**（byName）注入，如果找不到再按类型。
        
15. **`@Primary`**
    
    - **用法：** 放在 Bean 定义类上。
        
    - **作用：** 当有多个同类型的 Bean 时，优先注入被标记为 `@Primary` 的 Bean。
        

---

### 四、 Web 开发与请求处理 (Web & REST)

用于处理 HTTP 请求和参数映射。

16. **`@RequestMapping`**
    
    - **用法：** 放在类或方法上。
        
    - **作用：** 映射 HTTP 请求路径。如果在类上，则是该类所有方法的父路径。
        
17. **`@GetMapping`**
    
    - **用法：** 放在方法上。
        
    - **作用：** 处理 HTTP **GET** 请求（查询）。
        
18. **`@PostMapping`**
    
    - **用法：** 放在方法上。
        
    - **作用：** 处理 HTTP **POST** 请求（新增）。
        
19. **`@PutMapping`**
    
    - **用法：** 放在方法上。
        
    - **作用：** 处理 HTTP **PUT** 请求（更新）。
        
20. **`@DeleteMapping`**
    
    - **用法：** 放在方法上。
        
    - **作用：** 处理 HTTP **DELETE** 请求（删除）。
        
21. **`@PathVariable`**
    
    - **用法：** 放在参数前。
        
    - **作用：** 获取 URL 路径中的变量。例如 `/users/{id}` 中的 `id`。
        
22. **`@RequestParam`**
    
    - **用法：** 放在参数前。
        
    - **作用：** 获取 URL 查询参数（Query Param）。例如 `/users?name=john` 中的 `name`。
        
23. **`@RequestBody`**
    
    - **用法：** 放在参数前。
        
    - **作用：** 将 HTTP 请求体（Body）中的 JSON 数据反序列化为 Java 对象。
        
24. **`@ResponseBody`**
    
    - **用法：** 放在方法上（`@RestController` 默认包含了它）。
        
    - **作用：** 将方法的返回值序列化为 JSON/XML 写入 HTTP 响应体，而不是解析为视图。
        
25. **`@CrossOrigin`**
    
    - **用法：** 放在类或方法上。
        
    - **作用：** 解决跨域资源共享（CORS）问题，允许前端跨域访问接口。
        

---

### 五、 配置读取与环境 (Configuration & Properties)

用于读取配置文件（application.yml/properties）中的数据。

26. **`@Value`**
    
    - **用法：** 放在字段上。
        
    - **作用：** 注入配置文件中的单个属性值。例如：`@Value("${server.port}")`。
        
27. **`@ConfigurationProperties`**
    
    - **用法：** 放在类上。
        
    - **作用：** 将配置文件中前缀匹配的一组属性批量绑定到 Java Bean 上，比 `@Value` 更强大且类型安全。
        
28. **`@PropertySource`**
    
    - **用法：** 放在配置类上。
        
    - **作用：** 加载指定的 `.properties` 配置文件。
        

---

### 六、 全局异常与切面 (Global Handling & AOP)

29. **`@ControllerAdvice` / `@RestControllerAdvice`**
    
    - **用法：** 放在类上。
        
    - **作用：** 定义全局异常处理类。`@RestControllerAdvice` 也是返回 JSON。
        
30. **`@ExceptionHandler`**
    
    - **用法：** 放在方法上。
        
    - **作用：** 声明该方法用于捕获并处理特定的异常（如 `NullPointerException.class`）。
        

---

### 💡 快速对比表：参数接收注解

为了让你更清楚 Web 开发中最容易混淆的部分，这里有个小对比：

|**注解**|**来源**|**示例 URL**|**适用场景**|
|---|---|---|---|
|**@PathVariable**|URL 路径|`/user/101`|资源定位，如 ID|
|**@RequestParam**|URL 问号后|`/user?id=101`|筛选、分页、简单传参|
|**@RequestBody**|请求体 (Body)|(不可见，在 Payload 中)|提交复杂表单、JSON 对象|

---

我可以为你做的下一步：

如果你对其中某一组注解（例如 JPA 数据库相关 或 Web 请求参数处理）感兴趣，我可以为你写一段包含完整代码示例的 Demo，通过实际代码展示它们是如何配合工作的。你想看哪个方面的代码？