好的，完全没有问题。将 Spring Boot 框架的学习与实践结合，通过编程题来掌握是最高效的方式。
sudo lsof -i :8080

考虑到 Spring Boot 内容的广泛性，我将 300 道题分为两部分为您提供。今天先提供第一部分，共 150 题。这部分将覆盖 Spring Boot 的**核心概念、Web 开发 (MVC)、数据持久化 (MyBatis) 以及基础配置**，这些是开发一个标准 Web 应用必备的基石。

第二部分将会在您准备好后提供，会涵盖**AOP、安全(Security)、测试、Actuator、异步处理、消息队列以及微服务**相关的高级主题。

---

### **Spring Boot 核心编程 300 题 (Part 1 / 2)**

#### **第一部分：Spring 核心 & 依赖注入 (IoC/DI) (40题)**

这部分是 Spring 框架的灵魂，通过编码实践来彻底理解控制反转和依赖注入。

1. **组件扫描**: 创建一个 Spring Boot 项目，自定义一个 `com.example.components` 包，并在其中创建一个 `MyComponent` 类，使用 `@Component` 注解。验证程序启动后该组件被成功加载。
    ==`@PostConstruct`/构造方法里面写逻辑==
2. **三大组件注解**: 分别创建 `UserService`、`UserRepository` 和 `UserController`，并使用 `@Service`、`@Repository` 和 `@RestController` 注解它们。
    
3. **构造器注入**: 在 `UserService` 中注入 `UserRepository`，必须使用构造器注入的方式。解释为什么这是推荐的方式。
    ==1.用final修饰，唯一不可变==                        ==2.保证这个成员不为空==
4. **Setter 注入**: 重构上一题的代码，使用 `@Autowired` 和 Setter 方法进行注入。
    ==@Autowired在Setter方法上面==
5. **字段注入**: 再次重构，使用字段注入。讨论字段注入的优缺点。  
    ==直接在字段上标注@Autwired,但是因为类与框架耦合性高，测试难用，非常不推荐==
6. **`@Configuration`与`@Bean`**: 创建一个 `AppConfig` 配置类，在其中使用 `@Bean` 注解手动注册一个名为 `simpleBean` 的 `String` 类型的 Bean，并让其返回 "Hello, Bean!"。
    
7. **Bean 依赖**: 在 `AppConfig` 中创建两个 Bean：`beanA` 和 `beanB`。让 `beanA` 的创建依赖于 `beanB`，通过方法参数传递 `beanB` 来实现。
    
8. **`@Primary` 注解**: 创建一个 `MessageService` 接口和两个实现类 `EmailService` 和 `SmsService`。在一个 `NotificationService` 中注入 `MessageService`。使用 `@Primary` 注解，确保默认注入的是 `EmailService`。
    
9. **`@Qualifier` 注解**: 基于上一题，移除 `@Primary`。在 `NotificationService` 中，使用 `@Qualifier("smsService")` 来精确注入 `SmsService`。
    
10. **Bean 作用域 - Singleton**: 编写一个 `@Component`，在其中包含一个计数器。从 `ApplicationContext` 中两次获取该 Bean，调用计数器方法，验证两次获取的是同一个实例。
    
11. **Bean 作用域 - Prototype**: 将上一题的 Bean 的作用域改为 `@Scope("prototype")`，重复实验，并解释结果。
    
12. **`@Value`注入简单值**: 在 `application.properties` 中添加 `app.name=MyAwesomeApp`，然后在一个组件中使用 `@Value("${app.name}")` 将其注入到一个字段中。
    
13. **`@Value`注入默认值**: 尝试注入一个不存在的属性 `app.version`，并提供一个默认值 "1.0.0"，例如 `@Value("${app.version:1.0.0}")`。
    
14. **`@Value`注入 SpEL**: 使用 `@Value` 和 Spring Expression Language (SpEL) 注入一个系统属性，例如 `#{systemProperties['java.home']}`。
    
15. **Bean 生命周期 - `@PostConstruct`**: 创建一个 `DatabaseConnector` Bean，使用 `@PostConstruct` 注解一个 `init()` 方法，在其中打印 "Database connection initialized."。
    
16. **Bean 生命周期 - `@PreDestroy`**: 为 `DatabaseConnector` Bean 添加一个 `close()` 方法，并使用 `@PreDestroy` 注解，在其中打印 "Database connection closed."。
    
17. **Java 接口注入**: 创建一个 `List<String>` 类型的 Bean。然后在一个 Service 中注入这个 `List`。
    
18. **获取 ApplicationContext**: 实现 `ApplicationContextAware` 接口，以获取并持有一个对 `ApplicationContext` 的引用。
    
19. **`@Lazy` 注解**: 创建两个 Bean，A 依赖 B，B 依赖 A（循环依赖）。首先尝试启动，观察错误。然后在一个注入点使用 `@Lazy` 注解来解决这个循环依赖问题。
    
20. **条件化 Bean - `@ConditionalOnProperty`**: 在 `application.properties` 中添加 `feature.email.enabled=true`。创建一个 `EmailFeature` Bean，使其仅在该属性为 `true` 时才被创建。
    
21. **条件化 Bean - `@ConditionalOnClass`**: 创建一个 Bean，使其仅在 classpath 中存在 `com.fasterxml.jackson.databind.ObjectMapper` 这个类时才被创建。
    
22. **组件命名**: 使用 `@Component("myCustomName")` 为一个 Bean 指定一个自定义名称。
    
23. **`@Resource` 注解**: 使用 `@Resource(name = "beanB")` 替代 `@Autowired` 和 `@Qualifier` 来注入一个特定名称的 Bean。
    
24. **工厂 Bean**: 实现 `FactoryBean<T>` 接口，创建一个自定义的工厂 Bean，它负责生产和返回一个复杂的对象实例。
    
25. **`@Autowired` on `Optional`**: 在一个 Service 中注入一个 `Optional<AnotherService>`。演示 Spring 如何处理这种可选依赖。
    
26. **获取所有同类型 Bean**: 注入一个 `Map<String, MessageService>`，Spring 将会自动填充所有 `MessageService` 类型的 Bean，其中 key 是 Bean 的名称。
    
27. **Spring 事件 - 发布**: 创建一个自定义事件 `UserRegisteredEvent`。编写一个 `UserService`，在用户注册后，使用 `ApplicationEventPublisher` 发布此事件。
    
28. **Spring 事件 - 监听**: 编写一个 `EmailNotificationListener`，使用 `@EventListener` 注解一个方法，来监听并处理 `UserRegisteredEvent`。
    
29. **Bean 的排序**: 创建多个实现了 `org.springframework.core.Ordered` 接口的 Bean，并注入到一个 `List` 中。验证列表中的 Bean 是按顺序排列的。
    
30. **`@Order` 注解**: 使用 `@Order` 注解重构上一题，实现相同的排序效果。
    
31. **Spring 工具类**: 编写代码演示 `StringUtils.hasText()` 和 `CollectionUtils.isEmpty()` 的用法。
    
32. **环境抽象**: 使用 `Environment` 对象来读取 `application.properties` 中的属性，而不是 `@Value`。
    
33. **Profile 分组 (Java 11+)**: 在 `application.yml` 中使用 `spring.profiles.group` 将 `dev-db` 和 `dev-mq` 归为 `development` 组。
    
34. **关闭组件扫描**: 在 `@SpringBootApplication` 中使用 `scanBasePackages` 属性，将扫描范围限定在特定包，导致其他包的组件无法加载。
    
35. **手动注册 Bean**: 编写一个 `ApplicationRunner`，在其中通过 `GenericApplicationContext` 手动注册一个新的 Bean 实例。
    
36. **别名**: 在 `@Bean` 注解上使用 `name` 属性为同一个 Bean 定义多个别名。
    
37. **`ObjectProvider`**: 当需要一个非必需或延迟注入的 Bean 时，使用 `ObjectProvider<T>` 来代替直接注入 `T`。
    
38. **`@Import` 注解**: 创建一个独立的配置类，然后使用 `@Import` 将其导入到主配置类中。
    
39. **`@ImportResource`**: 创建一个传统的 Spring XML 配置文件 `beans.xml`，然后使用 `@ImportResource("classpath:beans.xml")` 将其加载到 Spring Boot 应用中。
    
40. **应用退出**: 编写一个 REST 端点，调用 `SpringApplication.exit()` 来优雅地关闭应用。
    

---

#### **第二部分：Spring Boot 配置与Web开发 (50题)**

这部分聚焦于 Spring Boot 的自动化配置、属性文件使用，以及构建 RESTful API 的核心技能。

41. **创建项目**: 访问 `start.spring.io`，创建一个包含 `Spring Web` 和 `Lombok` 依赖的 Maven/Gradle 项目。
    
42. **`@RestController`**: 创建一个 `HelloController`，提供一个 `GET` 请求的端点 `/hello`，返回字符串 "Hello, Spring Boot!"。
    
43. **`@RequestMapping`**: 在 `HelloController` 上使用 `@RequestMapping("/api")`，使其所有端点的路径都带上 `/api` 前缀。
    
44. **`@GetMapping` 与 `@PathVariable`**: 创建一个端点 `/users/{id}`，通过 `@PathVariable` 获取 `id` 并返回 "Fetching user with ID: {id}"。
    
45. **`@GetMapping` 与 `@RequestParam`**: 创建一个端点 `/articles`，通过 `@RequestParam` 接收一个可选的 `page` 参数和带默认值的 `size` 参数。
    
46. **`@PostMapping` 与 `@RequestBody`**: 创建一个 `User` DTO。编写一个 `POST` 端点 `/users`，使用 `@RequestBody` 接收 `User` 对象的 JSON 数据并返回。
    
47. **`@PutMapping`**: 编写一个 `PUT` 端点 `/users/{id}`，用于更新指定 ID 的用户信息。
    
48. **`@DeleteMapping`**: 编写一个 `DELETE` 端点 `/users/{id}`，用于删除指定 ID 的用户。
    
49. **`ResponseEntity`**: 重构 `POST /users` 端点，使用 `ResponseEntity` 返回创建的用户信息和 `201 Created` HTTP 状态码。
    
50. **统一响应结构**: 设计一个通用的 `ApiResponse<T>` 类，包含 `code`, `message`, `data` 字段，并用它来包装所有的 API 返回值。
    
51. **全局异常处理 - `@RestControllerAdvice`**: 创建一个全局异常处理器，使用 `@ExceptionHandler` 捕获 `NullPointerException` 并返回一个自定义的错误 `ApiResponse` 和 400 状态码。
    
52. **自定义异常**: 创建一个 `ResourceNotFoundException`。在全局异常处理器中专门为它创建一个处理方法，返回 404 状态码。
    
53. **数据校验 (Validation)**: 在 `User` DTO 的字段上添加 `javax.validation` 注解，如 `@NotEmpty`, `@Email`, `@Min`。
    
54. **启用校验**: 在 Controller 的 `POST /users` 方法的 `@RequestBody` 参数前添加 `@Valid` 注解，以触发校验。
    
55. **校验失败处理**: 在全局异常处理器中，添加一个方法来处理 `MethodArgumentNotValidException`，并返回包含所有字段校验错误的详细信息。
    
56. **自定义校验注解**: 创建一个自定义的校验注解，例如 `@PhoneNumber`，并为其实现一个 `ConstraintValidator`。
    
57. **分组校验**: 在 DTO 中定义两个校验分组接口 `OnCreate` 和 `OnUpdate`。在 `POST` 和 `PUT` 端点中分别启用不同的校验规则。
    
58. **YAML 配置文件**: 将 `application.properties` 文件转换为 `application.yml`，并配置相同的 `server.port` 和 `spring.application.name`。
    
59. **Profile 配置**: 创建 `application-dev.yml` 和 `application-prod.yml` 两个配置文件。在 `dev` 中将端口设置为 8081，在 `prod` 中设置为 80。通过激活不同的 Profile 来启动应用。
    
60. **`@ConfigurationProperties`**: 创建一个 `AppProperties` 类，使用 `@ConfigurationProperties(prefix = "app")` 将 `yml` 中 `app` 前缀下的所有配置项（如 `name`, `version`, `author`）批量注入到类属性中。
    
61. **`@ConfigurationProperties` 校验**: 为 `AppProperties` 类添加 JSR 303 校验注解，并在类上添加 `@Validated` 以启用配置属性的校验。
    
62. **静态资源处理**: 在 `src/main/resources/static` 目录下放置一个 `index.html` 文件，启动应用并访问 `http://localhost:8080/`。
    
63. **JSON 序列化定制 - `@JsonIgnore`**: 在 `User` DTO 的 `password` 字段上添加 `@JsonIgnore`，使其在返回的 JSON 中被忽略。
    
64. **JSON 序列化定制 - `@JsonProperty`**: 将 `User` DTO 的 `username` 字段在 JSON 中重命名为 `login_name`。
    
65. **JSON 日期格式化**: 在 `User` DTO 的 `Date` 类型字段上使用 `@JsonFormat`，将其格式化为 `yyyy-MM-dd HH:mm:ss`。
    
66. **文件上传**: 创建一个 `POST` 端点 `/upload`，能够接收 `MultipartFile` 并将其保存到服务器本地。
    
67. **文件下载**: 创建一个 `GET` 端点 `/download/{filename}`，能够从服务器返回一个文件供客户端下载。
    
68. **CORS 配置**: 使用 `@CrossOrigin` 注解在 Controller 或方法上，允许来自特定源的跨域请求。
    
69. **全局 CORS 配置**: 使用 `WebMvcConfigurer` 的 `addCorsMappings` 方法来配置全局的、更复杂的 CORS 规则。
    
70. **拦截器 Interceptor**: 实现一个 `HandlerInterceptor`，用于记录所有请求的处理时间。
    
71. **注册拦截器**: 实现 `WebMvcConfigurer` 接口，并重写 `addInterceptors` 方法来注册你自定义的拦截器。
    
72. **过滤器 Filter**: 实现一个 `javax.servlet.Filter`，用于在所有请求的最外层添加一个自定义的请求头。
    
73. **`@SpringBootApplication` 详解**: 尝试移除 `@SpringBootApplication` 注解，分别使用其包含的三个核心注解 `@SpringBootConfiguration`, `@EnableAutoConfiguration`, `@ComponentScan`，并解释它们的作用。
    
74. **排除自动配置**: 如果不想要 Spring Boot 的某个自动配置（例如 `DataSourceAutoConfiguration`），请使用 `@EnableAutoConfiguration(exclude=... )` 将其排除。
    
75. **自定义 `Banner`**: 在 `src/main/resources` 下创建一个 `banner.txt` 文件，自定义应用启动时显示的 Banner。
    
76. **`CommandLineRunner`**: 实现一个 `CommandLineRunner` Bean，在应用启动后执行一些初始化任务，例如打印加载的配置信息。
    
77. **`ApplicationRunner`**: 实现一个 `ApplicationRunner`，与上一题类似，但注意其参数类型与 `CommandLineRunner` 的不同。
    
78. **自定义 Starter (设计)**: 设计一个简单的 `my-log-spring-boot-starter`。思考其中需要包含哪些 `AutoConfiguration` 类和 `Properties` 类。
    
79. **Servlet、Filter、Listener 注册**: 使用 `@ServletComponentScan` 并结合 `@WebServlet`、`@WebFilter`、`@WebListener` 来注册原生的 Servlet 组件。
    
80. **自定义错误页面**: 在 `resources/static/error` 目录下创建 `404.html` 和 `500.html` 页面，当发生对应错误时，Spring Boot 会自动展示它们。
    
81. **路径匹配策略**: 在配置文件中设置 `spring.mvc.pathmatch.matching-strategy=ant-path-matcher`，体验与默认 `path-pattern-parser` 的不同。
    
82. **参数转换**: 实现一个 `Converter<String, LocalDate>`，将 "yyyy-MM-dd" 格式的字符串参数自动转换为 `LocalDate` 对象。
    
83. **DTO 与 Entity 转换**: 编写一个 `UserMapper` 类（非 MyBatis 的 Mapper），负责将 `User` 实体对象和 `UserDTO` 数据传输对象进行相互转换。
    
84. **异步请求**: 在 Controller 的一个方法上使用 `@Async` 注解，使其成为异步请求处理，并返回一个 `Callable<String>`。
    
85. **HTTP 客户端 - `RestTemplate`**: 在一个 Service 中注入 `RestTemplate`，并用它来调用一个外部的 GET API。
    
86. **HTTP 客户端 - `WebClient`**: (需要 `webflux` 依赖) 使用响应式的 `WebClient` 来执行与上一题相同的操作。
    
87. **自定义 `/favicon.ico`**: 在 `static` 目录下放置一个你自己的 `favicon.ico` 文件。
    
88. **Controller 方法重载**: 在同一个 Controller 中，为相同的路径 `/search` 创建两个方法，一个接收 `name` 参数，一个接收 `id` 参数。
    
89. **矩阵变量**: 创建一个端点 `/cars/{specs}`，其中 `specs` 可以是 `;color=red;year=2020`，并使用 `@MatrixVariable` 来提取这些值。（需要手动开启）
    
90. **Swagger/OpenAPI 集成**: 集成 `springdoc-openapi-ui` 依赖，为你的 REST API 自动生成交互式文档。
    

---

#### **第三部分：数据持久化 (Spring & MyBatis) (60题)**

这部分专注于使用 Spring Boot 和 MyBatis 连接数据库，实现增删改查、事务管理和动态 SQL 等核心功能。

91. **添加依赖**: 在项目中添加 `mybatis-spring-boot-starter` 和数据库驱动（如 `mysql-connector-java`）的依赖。
    
92. **数据源配置**: 在 `application.yml` 中配置数据库连接信息（`url`, `username`, `password`, `driver-class-name`）。
    
93. **创建实体类**: 创建一个 `User` 实体类，其属性对应数据库 `user` 表的字段（`id`, `username`, `email`）。
    
94. **创建 Mapper 接口**: 创建一个 `UserMapper` 接口，并使用 `@Mapper` 注解。
    
95. **XML 映射 - 查询**: 在 `resources/mapper/UserMapper.xml` 中，编写一个 `id` 为 `findById` 的 `select` 语句，根据 ID 查询用户信息。
    
96. **Mapper 接口方法**: 在 `UserMapper` 接口中添加 `User findById(Long id);` 方法。
    
97. **Service 层调用**: 在 `UserService` 中注入 `UserMapper`，并编写一个调用 `findById` 的方法。
    
98. **XML 映射 - 插入**: 在 XML 文件中编写一个 `insert` 语句，并设置 `useGeneratedKeys="true"` 和 `keyProperty="id"` 来返回自增主键。
    
99. **Mapper 接口方法 - 插入**: 在 `UserMapper` 中添加 `int insert(User user);` 方法，并验证 `user` 对象的 `id` 在插入后被成功赋值。
    
100. **XML 映射 - 更新**: 编写一个 `update` 语句，根据 `id` 更新用户的 `username`。
    
101. **XML 映射 - 删除**: 编写一个 `delete` 语句，根据 `id` 删除用户。
    
102. **`@Results` 与 `@Result`**: 不使用 XML，直接在 Mapper 接口的方法上使用 `@Select` 和 `@Results`、`@Result` 注解完成 `findById` 的功能。
    
103. **驼峰命名自动转换**: 在配置文件中开启 `mybatis.configuration.map-underscore-to-camel-case=true`，实现数据库下划线命名到 Java 驼峰命名的自动映射。
    
104. **注解映射 - 插入**: 使用 `@Insert` 和 `@Options` 注解实现插入用户并返回自增主键的功能。
    
105. **注解映射 - 更新/删除**: 分别使用 `@Update` 和 `@Delete` 注解实现更新和删除操作。
    
106. **动态 SQL - `<if>`**: 在 XML 中编写一个 `findUsers` 查询，可以根据传入 `User` 对象的 `username` 和 `email` 属性是否为空，来动态拼接 `WHERE` 查询条件。
    
107. **动态 SQL - `<where>`**: 使用 `<where>` 标签重构上一题的代码，使其能自动处理 `AND` 前缀。
    
108. **动态 SQL - `<set>`**: 编写一个动态更新语句，使用 `<set>` 和 `<if>` 标签，只更新 `User` 对象中不为空的字段。
    
109. **动态 SQL - `<foreach>`**: 编写一个 `findByIds` 查询，使用 `<foreach>` 标签实现 `WHERE id IN (...)` 的功能。
    
110. **`#` 与 `$` 的区别**: 编写两个查询，一个使用 `#{...}`，一个使用 `${...}`，传入相同的参数。解释它们在生成的 SQL 上的区别，并说明为什么应优先使用 `#{...}`。
    
111. **`@Param` 注解**: 在 Mapper 方法中，当有多个参数时，使用 `@Param` 注解为每个参数命名，以便在 XML 中引用。
    
112. **`resultMap`**: 在 XML 中定义一个复杂的 `resultMap`，处理一对一的关联查询（例如，查询订单及其关联的用户信息）。
    
113. **`collection` (一对多)**: 定义一个 `resultMap`，使用 `<collection>` 标签处理一对多的关联查询（例如，查询一个用户及其所有的订单列表）。
    
114. **`association` (多对一)**: 演示 `<association>` 的用法，与第 112 题类似。
    
115. **MyBatis 缓存**: 开启 MyBatis 的一级缓存（默认开启），在同一个 `SqlSession` 中连续执行两次相同的查询，并验证第二次查询是否命中了缓存。
    
116. **二级缓存**: 在 XML 中添加 `<cache/>` 标签开启二级缓存。创建两个不同的 `SqlSession` 执行相同的查询，并验证第二次查询命中了二级缓存。
    
117. **`@Transactional`**: 在 `UserService` 的一个方法上添加 `@Transactional` 注解。该方法内部连续执行两次数据库插入操作。
    
118. **事务回滚**: 修改上一题的代码，在两次插入操作之间手动抛出一个运行时异常，验证数据库中的两条记录都被回滚。
    
119. **`rollbackFor`**: 修改 `@Transactional` 注解，使用 `rollbackFor = Exception.class`，使得即使是受检异常（Checked Exception）也能触发事务回滚。
    
120. **事务传播 - `REQUIRED`**: 创建 `ServiceA` 和 `ServiceB`。`ServiceA` 的方法调用 `ServiceB` 的方法，两者都标注了 `@Transactional`。验证它们在同一个事务中执行。
    
121. **事务传播 - `REQUIRES_NEW`**: 将 `ServiceB` 的事务传播行为改为 `REQUIRES_NEW`。在 `ServiceA` 的方法中，如果 `ServiceB` 的方法抛出异常，验证 `ServiceA` 的操作是否也被回滚。
    
122. **只读事务**: 对于一个只执行查询操作的 Service 方法，使用 `@Transactional(readOnly = true)` 进行优化。
    
123. **`JdbcTemplate` - 查询**: 注入 `JdbcTemplate`，使用 `queryForObject` 方法查询单个值（例如，用户总数）。
    
124. **`JdbcTemplate` - 插入**: 使用 `JdbcTemplate` 的 `update` 方法执行一次插入操作。
    
125. **`RowMapper`**: 使用 `JdbcTemplate` 查询一个用户列表，并提供一个自定义的 `RowMapper` 将 `ResultSet` 映射到 `User` 对象。
    
126. **`NamedParameterJdbcTemplate`**: 使用 `NamedParameterJdbcTemplate` 执行一个带命名参数的 SQL 查询。
    
127. **集成分页插件**: 集成 PageHelper 插件，并编写一个 Service 方法，实现对用户列表的物理分页查询。
    
128. **类型处理器 (TypeHandler)**: 编写一个自定义的 `TypeHandler`，用于将数据库中的 `VARCHAR` 类型的 "Y/N" 与 Java 中的 `Boolean` 类型进行转换。
    
129. **MyBatis 拦截器 (Plugin)**: 编写一个 MyBatis 插件，拦截 `Executor` 的 `query` 方法，并在 SQL 执行前后打印日志。
    
130. **`@InsertProvider`**: 使用 `@InsertProvider` 和一个辅助的 SQL 构建类，以编程方式构建一个动态的插入 SQL。
    
131. **`@SelectProvider`**: 类似地，使用 `@SelectProvider` 构建一个复杂的动态查询 SQL。
    
132. **SQL 脚本初始化**: 在 `resources` 目录下创建 `schema.sql` 和 `data.sql`，让 Spring Boot 在启动时自动执行它们来初始化数据库表和数据。
    
133. **多数据源配置 (编程式)**: 编写代码，以编程方式配置两个不同的 `DataSource`，并将它们与不同的 `SqlSessionFactory` 关联。
    
134. **多数据源切换 (AOP)**: 使用 AOP 技术，通过自定义注解（如 `@DataSource("slave")`）在 Service 方法级别动态切换数据源。
    
135. **枚举处理**: 在实体类中使用枚举类型，并配置 MyBatis 正确地将其存储为数据库中的字符串或序数。
    
136. **`@ResultMap` 注解**: 在 Mapper 接口上使用 `@ResultMap` 注解，引用在 XML 中定义的 `resultMap` 的 ID。
    
137. **`<sql>` 与 `<include>`**: 在 XML 中使用 `<sql>` 标签定义一个可重用的 SQL 片段（例如，通用的列名列表），然后在多个查询中使用 `<include>` 标签引用它。
    
138. **`<choose>`, `<when>`, `<otherwise>`**: 编写一个查询，根据传入的参数值（如 `status`），使用 `<choose>` 标签执行不同的 SQL 逻辑。
    
139. **`<trim>`**: 使用 `<trim>` 标签自定义 `WHERE` 或 `SET` 子句的前后缀，实现比 `<where>` 和 `<set>` 更灵活的控制。
    
140. **调用存储过程**: 编写一个 MyBatis `select` 语句来调用数据库的存储过程。
    
141. **批量插入**: 在 Service 层循环调用 Mapper 的 `insert` 方法，并将其置于一个事务中。
    
142. **批量插入 (foreach)**: 在 XML 中使用 `<foreach>` 标签，实现一次性向数据库插入一个 `List<User>` 的高效批量操作。
    
143. **`useActualParamName`**: 配置 MyBatis 的 `useActualParamName` 为 `false`（默认为 `true`），观察此时不使用 `@Param` 会发生什么。
    
144. **延迟加载**: 配置并实现一个一对一关联的延迟加载（Lazy Loading）。
    
145. **MyBatis 日志**: 配置 MyBatis 输出执行的 SQL 语句和参数到控制台。
    
146. **事务超时**: 设置 `@Transactional(timeout = 5)`，并编写一个执行时间超过 5 秒的数据库操作，观察事务超时回滚。
    
147. **数据库方言**: 如果使用了分页插件，配置正确的数据库方言（如 `mysql`, `oracle`）。
    
148. **`@MapperScan`**: 在主启动类上使用 `@MapperScan` 注解，指定 Mapper 接口的基础包路径。
    
149. **XML 动态刷新**: (开发环境) 配置 Maven 或 Gradle 插件，使得修改 XML 映射文件后无需重启应用即可生效。
    
150. **SQL 注入演示**: 故意编写一个使用 `${}` 的 Mapper 方法，并传入恶意的参数（如 `' or '1'='1'` ），演示 SQL 注入的风险。
    

---

**学习建议：**

1. **从上到下，逐一实现**: 按照题单的顺序进行编码，这符合一个应用的构建逻辑：从核心容器 -> Web 接口 -> 数据库交互。
    
2. **理解“为什么”**: 不要只满足于代码能跑通。思考 Spring Boot 和 MyBatis 为什么要这么设计？它的自动化配置原理是什么？`@Transactional` 是如何通过 AOP 实现的？
    
3. **调试是最好的老师**: 大量使用断点调试，观察 Spring 容器中 Bean 的状态、Web 请求的处理流程、MyBatis 生成的最终 SQL 等。
    
4. **官方文档是终极指南**: 遇到问题时，优先查阅 Spring Boot 和 MyBatis 的官方文档，这是最权威、最准确的信息来源。
    

请先专注完成这 150 道编程题。当您觉得掌握得差不多了，我们再继续第二部分的高级主题。祝您学习愉快！
好的，我们继续 Spring Boot 核心编程题单的第二部分。

这部分将深入到**AOP、应用监控、安全、测试、缓存、异步任务、微服务生态集成**等高级和生产实践主题。完成这部分练习，将使你具备构建、测试、部署和维护生产级 Spring Boot 应用的能力。

---

### **Spring Boot 核心编程 300 题 (Part 2 / 2)**

#### **第四部分：Spring AOP (面向切面编程) (25题)**

AOP 是 Spring 的核心特性之一，用于解耦横切关注点。

151. **添加 AOP 依赖**: 在项目中添加 `spring-boot-starter-aop` 依赖。
    
152. **创建切面类**: 创建一个 `LogAspect` 类，并使用 `@Aspect` 和 `@Component` 注解它。
    
153. **`@Before` 通知**: 在 `LogAspect` 中，编写一个 `@Before` 通知，该通知在一个特定 Service 方法（如 `UserService.findUserById`）执行前打印日志。
    
154. **Pointcut 表达式 - `execution`**: 编写一个切点表达式，匹配 `com.example.service` 包下所有类的所有公共方法。
    
155. **`@AfterReturning` 通知**: 编写一个 `@AfterReturning` 通知，在方法成功返回后，获取并打印方法的返回值。
    
156. **`@AfterThrowing` 通知**: 编写一个 `@AfterThrowing` 通知，在方法抛出异常时，捕获并打印异常信息。
    
157. **`@After` 通知**: 编写一个 `@After` (finally) 通知，确保无论方法是正常返回还是抛出异常，都会执行一段资源清理的代码。
    
158. **`@Around` 通知**: 使用 `@Around` 环绕通知，实现一个方法性能监控的功能：记录方法的开始时间、结束时间，并计算总耗时。
    
159. **`ProceedingJoinPoint`**: 在 `@Around` 通知中，获取 `ProceedingJoinPoint` 对象，并调用其 `proceed()` 方法来执行目标方法。
    
160. **获取方法签名**: 在通知中，获取 `JoinPoint` 参数，并从中得到方法名、参数等信息。
    
161. **`@Pointcut` 复用**: 使用 `@Pointcut` 注解定义一个可复用的切点，然后在多个通知中引用它。
    
162. **自定义注解**: 创建一个自定义注解 `@LogExecutionTime`。
    
163. **注解切点**: 编写一个切点表达式，使其匹配任何标注了 `@LogExecutionTime` 注解的方法。
    
164. **切面顺序**: 创建两个切面，都作用于同一个方法。使用 `@Order` 注解来控制它们的执行顺序。
    
165. **AOP 失效场景 - `this` 调用**: 在一个 Service 类中，编写一个非 `public` 方法和一个 `public` 方法，让 `public` 方法调用 `this.privateMethod()`。验证作用于 `privateMethod` 的切面是否会生效，并解释原因。
    
166. **AOP 引入 (Introduction)**: 使用 `@DeclareParents` 为现有的 Service Bean 动态添加一个新的接口实现。
    
167. **AspectJ `within` 指示符**: 编写切点，匹配特定包 `com.example.controller` 下的所有方法。
    
168. **AspectJ `args` 指示符**: 编写切点，匹配方法参数为 `(String, ..)` 的方法（第一个参数是 String，后面任意）。
    
169. **AOP 实现事务 (概念)**: 解释 Spring 的 `@Transactional` 是如何基于 AOP 实现的。
    
170. **AOP 实现权限校验**: 设计一个切面，在 Controller 方法执行前检查用户是否拥有特定权限。
    
171. **AOP 参数绑定**: 在通知中，通过 `args(username)` 将切点匹配到的方法参数绑定到通知方法的参数上。
    
172. **CGLIB vs JDK 代理**: 编写一个实现了接口的 Service 和一个未实现接口的 Service。观察并解释 Spring AOP 分别为它们创建了哪种类型的代理。
    
173. **`EnableAspectJAutoProxy`**: 探索 `@EnableAspectJAutoProxy` 注解，并了解 `proxyTargetClass` 属性的作用。
    
174. **在 XML 中配置 AOP**: (作为了解) 创建一个 XML 配置文件，在其中定义一个切面、切点和通知。
    
175. **AOP 与 final 方法**: 尝试对一个 `final` 方法应用 AOP，观察结果并解释原因。
    

---

#### **第五部分：Actuator 应用监控与管理 (20题)**

Actuator 提供了生产级的应用监控和管理功能。

176. **添加 Actuator 依赖**: 在项目中添加 `spring-boot-starter-actuator` 依赖。
    
177. **访问健康检查端点**: 启动应用，访问 `/actuator/health` 端点，查看应用的健康状态。
    
178. **暴露所有端点**: 在 `application.yml` 中配置 `management.endpoints.web.exposure.include=*` 来暴露所有 Actuator端点。
    
179. **访问 Beans 端点**: 访问 `/actuator/beans`，查看 Spring 容器中所有 Bean 的信息。
    
180. **访问 Mappings 端点**: 访问 `/actuator/mappings`，查看所有 Controller 的 URL 路径映射。
    
181. **自定义 `info` 端点**: 在 `application.yml` 中添加 `info.app.name`, `info.app.version` 等信息，然后访问 `/actuator/info`。
    
182. **Git 版本信息**: 集成 `git-commit-id-plugin` 插件，使得 `/actuator/info` 端点能显示 Git 的提交信息。
    
183. **自定义健康指示器**: 实现 `HealthIndicator` 接口，创建一个自定义的健康检查逻辑（例如，检查与某个第三方服务的连接状态）。
    
184. **查看指标**: 访问 `/actuator/metrics` 查看所有可用的度量指标名称。
    
185. **查看特定指标**: 访问 `/actuator/metrics/{metric.name}`（例如 `/actuator/metrics/jvm.memory.used`）查看特定指标的详细信息。
    
186. **自定义指标 - `Counter`**: 注入 `MeterRegistry`，创建一个 `Counter`，用于统计某个 API 被调用的次数。
    
187. **自定义指标 - `Timer`**: 使用 `Timer` 来度量某个方法的执行时间。
    
188. **关闭应用**: 默认情况下 `/actuator/shutdown` 是禁用的。启用它，并尝试通过 `POST` 请求来关闭应用。
    
189. **修改管理端口**: 将 Actuator 端点的服务端口修改为一个与主应用不同的端口（例如 `management.server.port=9091`）。
    
190. **修改端点路径**: 将所有 Actuator 端点的基础路径从 `/actuator` 修改为 `/manage`。
    
191. **保护 Actuator 端点**: 集成 Spring Security，并配置只允许拥有 `ADMIN` 角色的用户访问 Actuator 端点。
    
192. **`@Endpoint` 自定义端点**: 创建一个类，使用 `@Endpoint(id="custom")`，在其中使用 `@ReadOperation` 和 `@WriteOperation` 创建你自己的 Actuator 端点。
    
193. **日志级别端点**: 访问 `/actuator/loggers` 端点，查看并动态修改应用的日志级别。
    
194. **线程快照**: 访问 `/actuator/threaddump` 端点，获取应用的线程快照信息。
    
195. **堆快照**: 访问 `/actuator/heapdump` 端点，下载 JVM 的堆快照文件。
    

---

#### **第六部分：Spring Security 安全控制 (35题)**

Spring Security 是构建安全应用的行业标准。

196. **添加 Security 依赖**: 添加 `spring-boot-starter-security` 依赖，启动应用，观察默认生成的登录页和安全策略。
    
197. **自定义内存用户**: 编写一个 `SecurityFilterChain` Bean，在其中配置一个 `InMemoryUserDetailsManager`，包含一个自定义的用户名、密码和角色。
    
198. **配置密码编码器**: 在配置中提供一个 `PasswordEncoder` Bean（例如 `BCryptPasswordEncoder`），并用它来加密内存用户的密码。
    
199. **自定义登录页**: 配置 Spring Security 使用你自己编写的 HTML 登录页面。
    
200. **URL 授权 - `requestMatchers`**: 配置 HTTP 安全规则，要求 `/api/admin/**` 路径需要 `ADMIN` 角色，`/api/user/**` 路径需要 `USER` 角色。
    
201. **URL 授权 - `permitAll`**: 配置 `/login` 和 `/api/public/**` 路径允许所有用户匿名访问。
    
202. **自定义 `UserDetailsService`**: 实现 `UserDetailsService` 接口，从数据库（使用 MyBatis Mapper）加载用户信息。
    
203. **方法级别安全**: 启用全局方法安全 (`@EnableGlobalMethodSecurity(prePostEnabled = true)`)。
    
204. **`@PreAuthorize`**: 在一个 Service 方法上使用 `@PreAuthorize("hasRole('ADMIN')")`，使其只能被 ADMIN 角色的用户调用。
    
205. **`@PostAuthorize`**: 使用 `@PostAuthorize`，根据方法返回值进行权限判断。
    
206. **获取当前用户信息**: 在 Controller 方法中，通过 `Authentication` 主体或 `@AuthenticationPrincipal` 注解获取当前登录用户的信息。
    
207. **JWT - 添加依赖**: 添加 JWT 相关的库依赖（如 `jjwt`）。
    
208. **JWT - 工具类**: 创建一个 `JwtTokenUtil` 类，包含生成 Token、从 Token 中解析用户信息、验证 Token 的方法。
    
209. **JWT - 登录接口**: 创建一个 `/api/auth/login` 端点，接收用户名密码，认证成功后，使用 `JwtTokenUtil` 生成一个 JWT 并返回给客户端。
    
210. **JWT - 请求过滤器**: 创建一个继承自 `OncePerRequestFilter` 的 JWT 认证过滤器。
    
211. **JWT - 过滤器逻辑**: 在过滤器中，从请求头 `Authorization: Bearer ...` 中获取 Token，验证它，如果有效，则构建一个 `Authentication` 对象并设置到 `SecurityContextHolder` 中。
    
212. **JWT - 集成过滤器**: 将你创建的 JWT 过滤器添加到 Spring Security 的过滤器链中。
    
213. **禁用 CSRF**: 对于 REST API，通常需要禁用 CSRF 防护。
    
214. **配置 Session 策略**: 将 Session 创建策略配置为无状态 (`STATELESS`)。
    
215. **自定义认证入口点**: 实现 `AuthenticationEntryPoint`，自定义当用户未认证时访问受保护资源所返回的响应（例如，返回 JSON 而不是重定向）。
    
216. **自定义访问拒绝处理器**: 实现 `AccessDeniedHandler`，自定义当用户权限不足时返回的响应。
    
217. **记住我 (Remember-Me)**: 为表单登录添加“记住我”功能。
    
218. **退出登录**: 配置并实现一个 `/logout` 端点。
    
219. **多种认证方式并存**: 配置应用同时支持表单登录和 JWT 认证。
    
220. **`@Secured`**: 使用 `@Secured("ROLE_ADMIN")` 注解来实现方法级别的安全控制。
    
221. **密码加密升级**: 编写一个服务，可以安全地将数据库中用旧加密方式（如 MD5）存储的密码升级为 BCrypt。
    
222. **OAuth2/OpenID Connect (概念与集成)**: (高级) 集成 `spring-boot-starter-oauth2-client`，实现通过 GitHub 或 Google 登录。
    
223. **`@RegisteredOAuth2AuthorizedClient`**: 在 Controller 中注入 `@RegisteredOAuth2AuthorizedClient` 来获取授权客户端的信息，例如 Access Token。
    
224. **资源服务器**: (高级) 将你的应用配置为一个资源服务器，能够验证外部授权服务器颁发的 JWT。
    
225. **`JwtAuthenticationConverter`**: 自定义一个 `Converter`，将 JWT 中的 `claims` 映射为 Spring Security 的 `GrantedAuthority`。
    
226. **跨域 (CORS) 与 Security**: 在 Spring Security 配置中，添加 `.cors()` 来集成你在 MVC 层配置的 CORS 策略。
    
227. **测试安全**: 编写测试用例，使用 Spring Security Test 的 `@WithMockUser` 来模拟已认证的用户，测试受保护的端点。
    
228. **自定义权限评估**: 实现 `PermissionEvaluator` 来自定义更复杂的、基于对象的权限控制逻辑（例如，检查用户是否有权操作某个特定的业务对象）。
    
229. **防止暴力破解**: 设计并实现一个登录失败锁定策略（例如，登录失败 5 次后锁定账户 10 分钟）。
    
230. **双因素认证 (2FA) (设计)**: 设计一个支持双因素认证（如短信验证码）的登录流程。
    

---

#### **第七部分：测试 (30题)**

编写测试是保证应用质量和可维护性的关键。

231. **添加测试依赖**: 确认项目中已包含 `spring-boot-starter-test` 依赖。
    
232. **单元测试 - Service**: 为一个 Service 类编写一个纯粹的 JUnit 5 单元测试，使用 Mockito 的 `@Mock` 和 `@InjectMocks` 来模拟其依赖（如 Repository）。
    
233. **集成测试 - `@SpringBootTest`**: 编写一个测试类，使用 `@SpringBootTest` 注解来加载完整的 Spring 应用上下文，并注入一个 Service Bean 进行测试。
    
234. **Web 层测试 - `@WebMvcTest`**: 为一个 Controller 编写测试，使用 `@WebMvcTest` 注解，它只加载 MVC 相关的组件。
    
235. **`MockMvc` - GET 请求**: 在 `@WebMvcTest` 中，注入 `MockMvc`，并用它来模拟一个 GET 请求，然后断言响应的状态码、内容类型和返回体。
    
236. **`MockMvc` - POST 请求**: 使用 `MockMvc` 模拟一个 `POST` 请求，发送 JSON 数据，并验证响应。
    
237. **`@MockBean`**: 在 `@WebMvcTest` 中，使用 `@MockBean` 来模拟 Controller 所依赖的 Service 层，使其返回预设的数据。
    
238. **数据层测试 - `@MybatisTest`**: 为一个 MyBatis Mapper 接口编写测试，使用 `@MybatisTest` 注解，它会配置一个内存数据库（如 H2）并只加载数据层相关的组件。
    
239. **JSON 测试 - `@JsonTest`**: 编写一个测试，使用 `@JsonTest` 和 `JacksonTester` 来验证一个 DTO 对象的序列化和反序列化是否符合预期。
    
240. **配置属性测试**: 测试一个 `@ConfigurationProperties` 类是否能正确从测试配置文件中加载属性。
    
241. **测试 Profile**: 创建一个 `application-test.yml`，并使用 `@ActiveProfiles("test")` 注解让测试类加载该配置。
    
242. **`@Sql` 脚本**: 在 `@MybatisTest` 中，使用 `@Sql` 注解在测试方法执行前运行一个 SQL 脚本来准备数据，在执行后运行另一个脚本来清理数据。
    
243. **`TestRestTemplate`**: 在 `@SpringBootTest(webEnvironment = ...)` 中，使用 `TestRestTemplate` 来发起真实的 HTTP 请求到测试中启动的应用。
    
244. **参数化测试**: 使用 JUnit 5 的 `@ParameterizedTest` 和 `@ValueSource` 为一个工具方法编写参数化的测试用例。
    
245. **断言库**: 使用 AssertJ 断言库 (`assertThat(...)`) 来编写更具可读性和表现力的断言。
    
246. **测试异步方法**: 编写测试来验证一个 `@Async` 方法的行为。
    
247. **测试 Actuator 端点**: 使用 `MockMvc` 或 `TestRestTemplate` 来测试一个自定义的 Actuator 端点是否返回正确的信息。
    
248. **测试事务回滚**: 在一个 `@Transactional` 的测试方法中，执行数据库操作后，验证数据是否已插入，测试结束后，再次验证数据是否已被自动回滚。
    
249. **测试切面**: 编写一个测试来验证你的 AOP 切面是否在目标方法执行时被正确触发。
    
250. **测试 Spring 事件**: 在测试中发布一个自定义事件，并验证对应的监听器是否被调用。
    
251. **测试异常处理**: 使用 `MockMvc` 发起一个会触发异常的请求，并验证全局异常处理器是否返回了预期的错误响应。
    
252. **测试数据校验**: 发送一个包含无效数据的 POST 请求，并验证是否返回了 400 错误和详细的校验失败信息。
    
253. **测试代码覆盖率**: 集成 Jacoco 插件，并生成代码覆盖率报告。
    
254. **切片测试 `@RestClientTest`**: 编写一个测试，使用 `@RestClientTest` 来测试你的 `RestTemplate` 客户端。
    
255. **`@DynamicPropertySource`**: 编写一个需要依赖外部服务（如 Docker 容器）的集成测试，并使用 `@DynamicPropertySource` 动态地设置数据库或消息队列的连接属性。
    
256. **测试调度任务**: 编写测试来验证一个 `@Scheduled` 任务的逻辑。
    
257. **测试过滤器**: 编写一个针对自定义 Servlet Filter 的测试。
    
258. **测试懒加载**: 在测试中验证 MyBatis 的延迟加载配置是否生效。
    
259. **端到端测试 (E2E)**: (概念) 设计一个使用 Selenium 或 Cypress 等工具的端到端测试场景。
    
260. **契约测试**: (概念) 了解并设计一个基于 Spring Cloud Contract 的消费者驱动的契约测试。
    

---

#### **第八部分：高级主题 & 生态集成 (40题)**

涵盖缓存、异步、调度、消息队列和容器化等。

261. **缓存 - 启用**: 使用 `@EnableCaching` 启用 Spring 的缓存抽象。
    
262. **缓存 - `@Cacheable`**: 在一个耗时的数据查询方法上添加 `@Cacheable`，并编写测试验证该方法在重复调用时只执行一次。
    
263. **缓存 - `@CachePut`**: 在数据更新方法上使用 `@CachePut`，确保更新数据库的同时也更新缓存。
    
264. **缓存 - `@CacheEvict`**: 在数据删除方法上使用 `@CacheEvict`，确保删除数据时缓存也被清除。
    
265. **缓存 - 条件化缓存**: 使用 `@Cacheable` 的 `condition` 或 `unless` 属性实现有条件的缓存。
    
266. **集成 Caffeine**: 添加 Caffeine 依赖，并配置 Spring Boot 使用它作为底层缓存实现。
    
267. **集成 Redis**: 添加 `spring-boot-starter-data-redis` 依赖，将缓存策略切换为 Redis。
    
268. **异步 - 启用**: 使用 `@EnableAsync` 开启异步方法执行。
    
269. **异步 - `@Async`**: 创建一个返回 `CompletableFuture<T>` 的异步 Service 方法，并在 Controller 中调用它。
    
270. **自定义异步执行器**: 配置一个自定义的 `ThreadPoolTaskExecutor` 作为异步方法调用的线程池。
    
271. **调度 - 启用**: 使用 `@EnableScheduling` 开启计划任务。
    
272. **调度 - 固定频率**: 创建一个使用 `@Scheduled(fixedRate = 5000)` 的任务，每 5 秒执行一次。
    
273. **调度 - Cron 表达式**: 创建一个使用 `@Scheduled(cron = "0 0 1 * * ?")` 的任务，每天凌晨 1 点执行。
    
274. **分布式锁**: (设计) 设计一个使用 Redis 或 Zookeeper 实现的分布式锁，以确保调度任务在集群环境中只执行一次。
    
275. **邮件发送**: 集成 `spring-boot-starter-mail`，编写一个 Service 发送一封简单的文本邮件。
    
276. **RabbitMQ - 发送消息**: 集成 `spring-boot-starter-amqp`，使用 `RabbitTemplate` 发送一条消息到一个队列。
    
277. **RabbitMQ - 接收消息**: 编写一个使用 `@RabbitListener` 注解的方法来监听并消费队列中的消息。
    
278. **Kafka - 发送消息**: 集成 `spring-kafka`，使用 `KafkaTemplate` 发送一条消息到一个主题。
    
279. **Kafka - 接收消息**: 编写一个使用 `@KafkaListener` 注解的方法来消费主题中的消息。
    
280. **WebSocket**: 集成 `spring-boot-starter-websocket`，实现一个简单的 WebSocket 消息推送服务器。
    
281. **Docker - `Dockerfile`**: 为你的 Spring Boot 应用编写一个多阶段构建的 `Dockerfile`。
    
282. **Docker - `build-image`**: 使用 Spring Boot 的 Maven/Gradle 插件（`spring-boot:build-image`）直接构建一个 OCI 容器镜像。
    
283. **优雅停机**: 在 `application.yml` 中配置 `server.shutdown=graceful`，并测试在应用关闭时，正在处理的请求能否完成。
    
284. **响应式编程 (WebFlux)**: (高级) 创建一个使用 Spring WebFlux 的响应式 Controller，返回 `Mono<T>` 或 `Flux<T>`。
    
285. **`WebClient`**: 使用 `WebClient` 以非阻塞方式调用外部 API。
    
286. **R2DBC**: (高级) 集成 `spring-boot-starter-data-r2dbc`，使用响应式的方式访问数据库。
    
287. **Spring Batch**: (高级) 创建一个简单的 Spring Batch 作业，实现从 CSV 文件读取数据，处理后写入数据库。
    
288. **Spring Cloud Gateway (设计)**: 设计一个使用 Spring Cloud Gateway 作为 API 网关的微服务架构。
    
289. **Spring Cloud OpenFeign (设计)**: 设计两个微服务，并使用 Feign 声明式地进行服务间的 REST 调用。
    
290. **Spring Cloud Config (设计)**: 设计如何使用 Spring Cloud Config Server 实现分布式系统的集中化配置管理。
    
291. **Sleuth & Zipkin (集成)**: 集成 Spring Cloud Sleuth 和 Zipkin，实现微服务间的分布式链路追踪。
    
292. **Retry**: 集成 `spring-retry`，为一个可能失败的操作（如调用外部 API）添加自动重试逻辑。
    
293. **Rate Limiting**: (设计) 使用 Resilience4j 或 Sentinel 等库，为你的 API 实现限流功能。
    
294. **Circuit Breaker**: (设计) 使用 Resilience4j 实现一个断路器，在下游服务持续失败时，能够快速失败并提供降级逻辑。
    
295. **GraphQL**: (高级) 集成 `spring-boot-starter-graphql`，创建一个简单的 GraphQL 查询端点。
    
296. **国际化 (i18n)**: 实现一个端点，能根据请求头 `Accept-Language` 返回不同语言的欢迎信息。
    
297. **自定义 FailureAnalyzer**: 实现一个 `FailureAnalyzer`，为特定的启动失败场景提供更友好、更具体的错误报告。
    
298. **应用打包**: 将应用分别打包成可执行的 JAR 和传统的 WAR 包。
    
299. **外部化配置**: 演示如何通过命令行参数覆盖 `application.yml` 中的配置项。
    
300. **持续学习**: 思考并列出你认为在 Spring Boot 生态中接下来需要学习的三个新技术或方向。
    

---

**最终建议：**

- **构建一个综合项目**: 将这两个部分的知识点串联起来，构建一个功能相对完整的个人项目。例如，一个包含用户认证、商品管理、订单处理、后台监控的简单电商系统。
    
- **阅读源码**: 在实践中遇到疑惑时，尝试去阅读 Spring Boot 的自动配置源码。这是从“会用”到“精通”的必经之路。
    
- **关注社区**: 关注 Spring 官方博客、社区动态和版本更新，保持知识的先进性。
    

恭喜你完成了这份包含 300 道编程题的挑战！坚持下来，你的 Spring Boot 开发能力必将达到一个新的高度。祝你成功！