这份500题的题单专为构建**高可用后端架构、深入框架源码以及应对严苛的企业级（如金融、国企、大型互联网）研发需求**而设计。

题单从 Spring 基础深入到 Boot 自动装配，再到底层数据访问（JDBC/MyBatis/锁机制）、中间件（Redis/MQ）以及容器化部署与复杂业务场景设计。建议结合源码阅读与本地 Docker 环境实战。

---

### **准备工作：环境与工具**

* **开发环境**: JDK 17/21, IntelliJ IDEA, Maven/Gradle。
* **部署环境**: 建议在 Linux (Ubuntu) 或 WSL 2 下结合 Docker / Docker Compose 进行中间件的快速部署与网络调试。
* **必备工具**: Postman/Apifox, DataGrip/Navicat, Redis Desktop Manager, JProfiler/Arthas。

---

### **模块一：Spring 核心基石：IoC 与 AOP (50题)**

**核心知识点**: Bean 生命周期、三级缓存与循环依赖、动态代理底层的字节码技术。

**IoC 容器与 Bean 管理 (1-25)**

1. 手写一个简易的 IoC 容器，通过反射解析 `@Component` 并完成实例化。
2. 简述 `BeanFactory` 与 `ApplicationContext` 的本质区别。
3. 详细追踪一个单例 Bean 从实例化、属性填充、初始化到销毁的完整生命周期。
4. 解释 `@Autowired` 和 `@Resource` 在按类型与按名称注入时的匹配逻辑。
5. 演示如何通过实现 `BeanPostProcessor` 接口，在 Bean 初始化前后动态修改其属性。
6. 使用 `FactoryBean` 接口，手动向 Spring 容器中注册一个复杂的第三方对象。
7. 分析 Spring 解决单例 Bean 循环依赖的"三级缓存"机制。
8. 为什么 Spring 无法解决构造器注入的循环依赖？编写代码验证并捕获异常。
9. 解释 `@Configuration` 类中 `@Bean` 方法调用另一个 `@Bean` 方法时，为什么不会产生多个实例？（CGLIB 增强）。
10. 通过 `@Conditional` 系列注解实现按环境变量动态加载不同的 Bean。
11. 深入理解 `@Configuration` 的 `proxyBeanMethods` 属性：设为 false 后对 Bean 调用行为有何影响？
12. 分析 `@ComponentScan` 的扫描过滤规则：使用 `includeFilters` 和 `excludeFilters` 自定义扫描策略。
13. 探究 `@Import` 注解的三种用法：直接导入配置类、实现 `ImportSelector`、实现 `ImportBeanDefinitionRegistrar`。
14. 理解 `@Scope` 注解：分别演示 singleton、prototype、request、session 作用域的 Bean 创建时机。
15. 深入 `Environment` 抽象：通过 `@PropertySource` 加载外部 `.properties` 文件，并使用 `Environment` API 读取。
16. 掌握 Spring 事件机制：自定义事件类、通过 `ApplicationEventPublisher` 发布事件、`@EventListener` 异步消费。
17. 对比 `@EventListener` 与实现 `ApplicationListener` 接口两种事件监听方式的异同。
18. 探究 Spring 的 `MessageSource` 国际化支持：配置多语言资源文件并动态切换 Locale。
19. 学习 `ResourceLoader` 和 `Resource` 接口：加载 classpath、文件系统、HTTP 远程等不同来源的资源文件。
20. 了解 `BeanDefinitionRegistryPostProcessor`：在 Bean 定义注册阶段动态添加或修改 BeanDefinition。
21. 分析 `@Primary` 和 `@Qualifier` 在解决同类型多 Bean 注入歧义时的区别与配合使用。
22. 探究 `@Lazy` 注解的原理：懒加载 Bean 的代理对象是如何在第一次调用时才触发真正初始化的？
23. 深入 `@DependsOn` 注解：控制 Bean 的初始化顺序，解决无显式依赖但需要先后启动的场景。
24. 如何通过 `ObjectProvider<T>` 实现延迟注入和可选注入，规避 `NoSuchBeanDefinitionException`？
25. 综合实战：基于 IoC 容器的事件机制，设计一个「用户注册成功」后异步发送邮件和短信通知的解耦方案。

**AOP 动态代理与切面 (26-50)**
26. 使用 JDK 动态代理手写一个方法耗时统计的代理类。
27. 使用 CGLIB 编写同样的代理类，并对比两者在底层生成字节码的差异。
28. 在 Spring 中定义一个 `@Aspect` 切面，拦截所有标注了自定义注解 `@LogAction` 的方法。
29. 验证 AOP 的同类方法调用失效问题（自调用陷阱），并通过 `AopContext.currentProxy()` 解决。
30. 深入理解多次代理：如果一个 Bean 同时被事务 AOP 和日志 AOP 增强，拦截器的执行顺序是如何决定的？
31. 掌握切点表达式语法：分别编写匹配「指定包下所有 public 方法」「带指定注解的类」「指定参数类型」的切点。
32. 对比 `@Before`、`@After`、`@AfterReturning`、`@AfterThrowing`、`@Around` 五种 Advice 的执行时机与异常处理差异。
33. 通过 `@Around` 环绕通知实现一个接口级别的分布式限流注解 `@RateLimiter`。
34. 编写切面在方法执行前后自动打印入参和出参的 JSON 日志（注意大对象的截断处理）。
35. 通过 `ProceedingJoinPoint` 获取目标方法的 Method 对象、参数列表、以及目标 Bean 实例。
36. 探究 AOP 的 `exposeProxy` 配置：为什么需要将代理暴露到 ThreadLocal 中？
37. 使用 AOP 实现一个简单的「操作日志」记录系统：记录操作人、IP、方法名、参数、耗时、返回值。
38. 探究 Spring AOP 与 AspectJ 的关系：对比两者的实现原理、编译期织入与运行时织入的区别。
39. 通过 AOP 实现方法级别的「权限校验」：在方法执行前检查用户是否拥有指定角色或权限。
40. 设计一个注解 `@RepeatSubmit`，利用 AOP 和 Redis 防止用户在短时间内重复提交同一请求。
41. 探究多个切面作用于同一连接点时，`@Order` 注解如何控制执行顺序？
42. 为什么 Spring AOP 不能拦截 `private` 方法和 `final` 方法？从代理原理角度深入解释。
43. 演示引入（Introduction）增强：使用 `@DeclareParents` 让现有 Bean 动态实现额外接口。
44. 分析 `@Transactional` 注解的实现原理：Spring 如何通过 AOP 代理实现事务的开启、提交和回滚？
45. 编写代码验证 AOP 代理对象内部的 this 指向：为什么在增强方法内部调用另一个未被拦截的方法时切面不会生效？
46. 探究 `Advisor` 和 `Advice` 的关系：`PointcutAdvisor` 是如何将切点和通知组合在一起的？
47. 实现一个「方法缓存」注解 `@MethodCache`：基于 AOP 环绕通知，先查 Redis 缓存，命中则直接返回，未命中则执行方法并写入缓存。
48. 实现一个「接口熔断」注解 `@CircuitBreaker`：当目标方法在时间窗口内失败次数超过阈值时，直接抛出熔断异常，不再调用真实方法。
49. 对比 JDK 动态代理和 CGLIB 的性能开销：编写基准测试对比两种代理方式在创建代理和调用代理方法时的耗时差异。
50. 综合实战：设计一个完整的「接口监控」AOP 方案，包含 QPS 统计、P99 耗时记录、异常率统计，并通过日志定时输出监控面板。

---

### **模块二：Spring Boot 核心原理与 Web MVC (50题)**

**核心知识点**: 自动装配原理、Starter 编写、内嵌 Web 容器、全局异常与拦截器。

**自动装配与内嵌容器 (51-75)**
51. 剖析 `@SpringBootApplication` 注解，解释 `@EnableAutoConfiguration` 的工作流。
52. 在 `META-INF/spring.factories` (或 `org.springframework.boot.autoconfigure.AutoConfiguration.imports`) 中注册自定义配置类。
53. 从零手写一个 `my-redis-spring-boot-starter`，实现根据属性文件自动配置连接池。
54. Spring Boot 是如何启动内嵌 Tomcat 的？追踪 `TomcatServletWebServerFactory` 的源码。
55. 通过配置参数替换 Spring Boot 默认的 Tomcat 容器为 Undertow，并进行简单的并发压测对比。
56. 编写自定义的 `FailureAnalyzer`，在 Spring Boot 启动失败时输出友好的错误排查信息。
57. 详细分析 `application.yml` / `application.properties` 的加载优先级：外部配置、命令行参数、环境变量等谁先谁后？
58. 掌握 Spring Boot 多环境切换：通过 `spring.profiles.active` 加载 dev、test、prod 配置的最佳实践。
59. 探究 `@ConfigurationProperties` 的使用：将一组相关配置映射为 Java 对象，并配置 IDEA 的 Metadata 自动提示。
60. 对比 `@Value` 与 `@ConfigurationProperties` 在批量属性绑定时各自的优劣。
61. 编写自定义的 `PropertySourceLoader`，扩展 Spring Boot 支持从 JSON 或 YAML 文件中加载自定义配置格式。
62. 了解 Spring Boot 的 `ApplicationRunner` 和 `CommandLineRunner`：在应用启动完成后执行初始化逻辑。
63. 探究 `SpringApplication` 的构建过程：设置 banner、设置运行模式、添加初始化器和监听器。
64. 掌握 Actuator：开启 `/actuator/health`、`/metrics`、`/env` 等端点，并自定义 HealthIndicator 健康检查。
65. 使用 Micrometer + Prometheus + Grafana 搭建 Spring Boot 应用的监控面板，实时展示 JVM 指标和业务指标。
66. 探究 Spring Boot 的 `spring.factories` 文件向 `AutoConfiguration.imports` 迁移的原因和机制变化。
67. 了解 Spring Boot 的类加载机制：`LaunchedURLClassLoader` 如何实现 Fat Jar 中嵌套 Jar 的加载？
68. 编写自定义的 `SpringApplicationRunListener`，监听应用启动全生命周期的各个阶段事件。
69. 掌握 `@EnableConfigurationProperties` 的使用方式，对比直接在配置类上标注该注解与在属性类上标注 `@ConfigurationProperties` 的区别。
70. 通过 `BootstrapRegistry` 和 `ConfigDataLocationResolver` 扩展，实现从外部配置中心（如 Nacos/Apollo）加载配置。
71. 探究 Spring Boot 3.x 的 AOT (Ahead-of-Time) 编译机制：通过 GraalVM Native Image 将应用编译为原生可执行文件。
72. 了解内嵌 Web 容器的定制化：通过 `WebServerFactoryCustomizer` 修改 Tomcat 的连接器、线程池等核心参数。
73. 配置 Spring Boot 应用的优雅关闭（Graceful Shutdown）：确保收到 SIGTERM 后等待正在处理的请求完成后再退出。
74. 探究 Spring Boot 的 `@TestConfiguration` 和切片测试注解（`@WebMvcTest`, `@DataJpaTest`）的使用。
75. 综合实战：编写一个完整的 `my-elasticsearch-spring-boot-starter`，包含自动配置、健康检查、指标收集、以及属性提示。

**Web MVC 与 RESTful 实践 (76-100)**
76. 梳理 Spring MVC 的核心工作流：从 `DispatcherServlet` 接收请求到 `ViewResolver` 渲染。
77. 编写一个通用的 `@RestControllerAdvice` 全局异常处理器，统一封装业务异常与系统异常。
78. 实现 `HandlerInterceptor` 拦截器，进行请求的签名校验，并与 Filter 的执行时机进行对比。
79. 配置 `HttpMessageConverter`，自定义 JSON 的序列化规则（如屏蔽敏感字段、时间格式化）。
80. 处理跨域问题：在 Spring Boot 中通过 `@CrossOrigin` 和全局 `CorsFilter` 解决 CORS 限制。
81. 使用 `@Validated` 和 `@Valid` 对请求参数进行校验，并自定义校验注解（如 `@IdCard` 身份证校验）。
82. 处理 BindingResult 和 MethodArgumentNotValidException，将参数校验失败信息统一封装为结构化错误响应。
83. 配置文件上传：限制上传大小、格式白名单，并通过 MultipartFile 实现分片上传与断点续传。
84. 探究 `@RequestBody` 和 `@RequestParam` 的使用场景区别，以及 `@RequestPart` 在混合表单上传中的应用。
85. 实现 `DeferredResult` 和 `Callable` 异步处理 HTTP 请求，提升容器线程吞吐量。
86. 对比 Spring MVC 的同步模型与 Servlet 3.0 异步模型：`AsyncContext` 是如何将请求线程归还给容器的？
87. 使用 `ResponseBodyEmitter` 和 `SseEmitter` 实现服务端推送（Server-Sent Events），向前端实时推送任务进度。
88. 掌握 `@SessionAttributes` 和 `@CookieValue` 的使用，在 Controller 间共享 Session 数据。
89. 配置 `PathMatchConfigurer` 实现 URL 路径的忽略大小写匹配和自定义后缀模式匹配。
90. 使用 `ContentNegotiationConfigurer` 配置同一接口同时支持返回 JSON 和 XML 格式。
91. 理解 `HandlerMapping` 与 `HandlerAdapter` 的关系：DispatcherServlet 如何找到处理请求的 Controller 方法？
92. 探究 `HttpMessageConverter` 的注册顺序：客户端 Accept Header 如何决定最终使用哪个 Converter？
93. 实现自定义的 `ArgumentResolver`：解析带有自定义注解的方法参数，如从 Header 中自动提取 `@CurrentUser` 当前登录用户对象。
94. 实现自定义的 `ReturnValueHandler`：统一处理带有特定注解的方法返回值，如对 `@ApiResult` 标注的方法自动包装为统一响应体。
95. 配置 Spring Boot 的嵌入式 Web 容器的并发连接数、请求超时时间和连接超时时间。
96. 探究 Spring MVC 中 Filter、Interceptor、`@ControllerAdvice`、AOP 的执行顺序：一个请求从入口到出口经过了哪些处理链？
97. 深入理解 `OncePerRequestFilter`：为什么需要保证一次请求只执行一次过滤逻辑？
98. 通过 `WebMvcConfigurer` 配置静态资源映射、路径匹配、跨域、拦截器注册等全局 MVC 行为。
99. 探究 Spring Boot 3.x 中的 ProblemDetail（RFC 7807）规范：标准化错误响应的结构和字段。
100. 综合实战：实现一个 API 网关的过滤器链路——包含 JWT 鉴权 Filter、请求日志 Interceptor、限流 AOP 切面、统一异常 ControllerAdvice，并测试它们的执行顺序。

---

### **模块三：数据访问底座：JDBC、MyBatis 与元数据 (75题)**

**核心知识点**: 数据库连接管理、动态 SQL、MyBatis 拦截器、元数据提取。

**JDBC 深度探索与驱动加载 (101-125)**
101. 不依赖框架，使用原生 JDBC 编写一个完整的增删改查逻辑，并在 `finally` 块中正确释放资源。
102. 深度剖析 `java.sql.DatabaseMetaData`：编写程序连接 MySQL，提取指定数据库下所有表的表名、列名、数据类型及注释。
103. 进一步提取元数据：获取某张表的主键信息及所有索引结构。
104. 突破默认类加载器：利用 `URLClassLoader` 动态加载外部指定路径的 JDBC 驱动 Jar 包，并成功建立数据库连接。
105. 在多数据源场景下，通过破坏双亲委派机制，实现不同版本的 MySQL 驱动隔离加载。
106. 分析并配置 HikariCP 连接池的核心参数（`maximumPoolSize`, `connectionTimeout`, `maxLifetime`），模拟连接泄露并排查。
107. 使用 JDBC 批处理 `executeBatch` 批量插入 10 万条数据，对比逐条插入的性能差距，分析原因。
108. 理解 `PreparedStatement` 预编译原理：MySQL 服务端是如何缓存执行计划的？`useServerPrepStmts` 参数的作用是什么？
109. 探究 `Statement` 与 `PreparedStatement` 的安全性差异：用代码演示 SQL 注入攻击并通过预编译防御。
110. 使用 `ResultSet` 的 `setFetchSize` 和游标模式遍历百万级数据，避免 OOM 的同时保持查询效率。
111. 深入 `ResultSetMetaData`：动态获取查询结果的列结构信息并构建通用的查询结果导出工具。
112. 分析 JDBC 事务提交模式：`autoCommit` 为 true 和 false 时对锁持有时间和并发性能的影响。
113. 利用 `Savepoint` 实现事务中的部分回滚：在批量扣款操作中，某一条扣款失败时可回退到指定保存点。
114. 深入 JDBC 驱动的 `DriverManager` 与 `ServiceLoader`（SPI）机制：驱动是如何通过 `META-INF/services/java.sql.Driver` 自动注册的？
115. 编写一个简易的连接池：使用阻塞队列维护一定数量的数据库连接，实现 `getConnection()` 和 `releaseConnection()`。
116. 通过 `DatabaseMetaData` 提取数据库的所有外键关系，并结合 `ResultSetMetaData`，自动生成 ER 图的数据结构。
117. 模拟数据库连接泄露：故意不释放连接，通过 `SHOW PROCESSLIST` 观察连接数增长，并使用 Arthas 定位泄漏代码位置。
118. 探究 `RowSet` 接口：`JdbcRowSet`、`CachedRowSet` 的区别与使用场景。
119. 掌握 HikariCP 的连接存活检测机制：`connectionTestQuery` 和 `validationTimeout` 如何保证拿到的连接是可用的？
120. 通过 `CallableStatement` 调用 MySQL 存储过程，处理 IN/OUT/INOUT 三种参数模式。
121. 探究 JDBC 4.x 的新特性：`try-with-resources` 自动关闭资源、`Wrapper` 模式解包装特定驱动实现。
122. 在多数据源场景下，基于 ThreadLocal 实现动态数据源路由切换（读写分离）。
123. 深入 `DataSource` 接口体系：对比 `BasicDataSource`、`DruidDataSource`、`HikariDataSource` 的设计思路和监控能力。
124. 利用 Druid 的 Filter 机制实现慢 SQL 监控告警：自定义 Filter 统计 SQL 执行时间和频率。
125. 综合实战：编写一个数据库元数据导出工具，连接任意 MySQL 数据库后，将库中所有表结构、索引、外键信息导出为 Markdown 格式的数据字典文档。

**MyBatis 原理与进阶 (126-175)**
126. 解释 MyBatis 中 `#{}` 和 `${}` 的底层区别，并编写代码演示 `${}` 如何导致 SQL 注入。
127. 追踪 MyBatis 源码：Mapper 接口没有实现类，是如何被 Spring 注入并执行的？（动态代理与 `MapperFactoryBean`）。
128. 熟练运用动态 SQL：编写包含 `<if>`, `<choose>`, `<trim>`, `<foreach>` 的复杂多条件组合查询。
129. 手写一个 MyBatis 插件（`Interceptor`），拦截 `StatementHandler`，自动在所有 Update 语句执行前填充 `update_time` 字段。
130. 编写另一个插件，在执行慢查询时自动在控制台打印完整的、带参数值的 SQL 语句。
131. MyBatis 的一级缓存（SqlSession 级别）和二级缓存（Mapper 级别）在多表联查时容易引发什么脏数据问题？如何避免？
132. 深入 `ResultMap`：处理一对一（`<association>`）、一对多（`<collection>`）的嵌套结果映射，以及 N+1 问题的解决。
133. 探究 MyBatis 的延迟加载（Lazy Loading）机制：`aggressiveLazyLoading` 和 `lazyLoadTriggerMethods` 的配合使用。
134. 对比 MyBatis 的嵌套查询（Nested Select）与嵌套结果（Nested Result）：优劣与性能分析。
135. 使用 MyBatis 的 `<discriminator>` 鉴别器实现根据某个字段值动态选择不同的映射规则。
136. 掌握 MyBatis 的 `@SelectProvider`、`@InsertProvider`、`@UpdateProvider`、`@DeleteProvider` 注解：通过 Java 类动态构建 SQL。
137. 熟练使用 MyBatis-Plus 的 `BaseMapper<T>` 接口：自动 CRUD、条件构造器 `QueryWrapper` 和 `UpdateWrapper`。
138. 深入 MyBatis-Plus 的分页插件原理：如何拦截 SQL 并改写为 count + limit 的两段式分页查询？
139. 了解 MyBatis-Plus 的乐观锁插件：通过 `@Version` 注解配合 `update(entity, wrapper)` 实现并发安全的更新。
140. 探究 MyBatis-Plus 的逻辑删除：`@TableLogic` 注解的底层实现——如何将 DELETE 语句转换为 UPDATE？
141. 使用 MyBatis-Plus 的自动填充功能：`MetaObjectHandler` 实现 `create_time` / `update_time` 的自动维护。
142. 探究 MyBatis-Plus 代码生成器：基于表结构自动生成 Entity、Mapper、Service、Controller 层的代码模板。
143. 深入 MyBatis 的 `TypeHandler`：自定义处理 JSON 字段与 Java 对象的自动转换。
144. 使用枚举 TypeHandler：优雅地处理数据库字段与 Java 枚举类型之间的映射。
145. 探究 MyBatis 的参数解析：`ParamNameResolver` 如何获取 Mapper 方法的参数名？
146. 深入 MyBatis 的 `Executor` 体系：`BaseExecutor`、`SimpleExecutor`、`ReuseExecutor`、`BatchExecutor` 的区别。
147. 分析 MyBatis 和 MyBatis-Spring 整合后 SqlSession 的生命周期管理：如何利用 `SqlSessionTemplate` 保证线程安全？
148. 探究 MyBatis 二级缓存的实现：`Cache` 接口及内置的 LRU、FIFO、SOFT、WEAK 策略。
149. 如何自定义 MyBatis 的二级缓存实现？集成 Redis 作为分布式二级缓存替换默认的本地缓存。
150. 了解 MyBatis 的 `@MapperScan` 注解：`MapperScannerRegistrar` 是如何扫描指定包下的 Mapper 接口并注册到容器的？
151. 探究 `@Param` 注解的作用原理：多参数方法在映射 XML 时为何需要指定参数名称？
152. 使用 MyBatis 执行存储过程：编写 XML 映射调用带有多个 OUT 参数的 MySQL 存储过程。
153. 掌握 MyBatis 的 `<bind>` 标签：在动态 SQL 中创建变量以简化模糊查询和多数据库函数适配。
154. 使用 `<sql>` 和 `<include>` 标签抽取可复用的 SQL 片段，避免重复编写相同的查询列和条件。
155. 探究 MyBatis 中 `#{}` 和 `${}` 在 `ORDER BY` 和 `GROUP BY` 场景下的使用：为什么动态排序只能用 `${}`？如何安全处理？
156. 深入 MyBatis 的日志模块：如何让 MyBatis 将完整的 SQL 日志输出到指定文件而不是控制台？
157. 使用 MyBatis 的 `RowBounds` 和 `ResultHandler` 处理超大结果集：逐条处理而非一次性加载全部数据到内存。
158. 探究 MyBatis 对 JDBC `fetchSize` 的支持：通过设置 `defaultFetchSize` 实现流式查询防止 OOM。
159. 编写 MyBatis 插件实现数据脱敏：拦截 `ResultSetHandler`，对 `phone`、`id_card`、`email` 等敏感字段自动脱敏。
160. 编写 MyBatis 插件实现分表路由：根据租户 ID 动态修改表名，实现简单的水平分表。
161. 探究 MyBatis 的 `@MapKey` 注解：将查询结果直接映射为 `Map<String, Entity>` 的结构。
162. 探究 MyBatis 返回 `Map<String, Object>` 时的字段名大小写问题与 `mapUnderscoreToCamelCase` 配置的作用。
163. 如何使用 MyBatis-Plus 的 `LambdaQueryWrapper` 避免硬编码字段名？探究其类型安全的实现原理（SerializedLambda）。
164. 掌握 MyBatis-Plus 的主键策略：AUTO、INPUT、ASSIGN_ID（雪花算法）、ASSIGN_UUID 的区别。
165. 探究 MyBatis-Plus 的 ActiveRecord 模式：实体类继承 `Model<T>` 后直接调用 CRUD 方法。
166. 使用 MyBatis-Plus 的 `ServiceImpl` 和 `IService` 接口：链式调用和批量操作的便捷封装。
167. 对比 MyBatis 和 JPA/Hibernate 的核心设计理念：SQL 可控性 vs ORM 自动化。
168. 探究 MyBatis 对 JSON 字段的原生支持（3.5.x+）：如何结合 MySQL 的 JSON 类型优雅地存储与查询非结构化数据。
169. 编写测试用例验证 MyBatis 一级缓存的命中条件：相同 SqlSession、相同 MappedStatement、相同参数。
170. 探究 MyBatis 二级缓存的脏数据风险：多节点部署下本地缓存不一致的解决方案。
171. 如何在一个项目中间接混合使用 MyBatis 和 JPA，各自管理独立的 DataSource 和事务管理器？
172. 掌握 MyBatis 的 `@Options` 注解：配置 `useGeneratedKeys` 和 `keyProperty` 回填自增主键。
173. 使用 MyBatis 的 `databaseIdProvider` 编写跨数据库兼容的 SQL 映射（MySQL / Oracle / PostgreSQL）。
174. 探究 MyBatis 的 `@ConstructorArgs` 注解：如何将查询结果直接映射到没有无参构造器的不可变 DTO？
175. 综合实战：在一个「订单管理」场景中，使用 MyBatis-Plus 结合自定义插件，同时实现：逻辑删除、自动时间填充、乐观锁更新库存、慢 SQL 监控告警、敏感字段脱敏。

---

### **模块四：数据库底层与高并发事务锁机制 (50题)**

**核心知识点**: 事务传播行为、MVCC 机制、行锁/表锁/间隙锁、SQL 调优。

**事务与 Spring 事务控制 (176-200)**
176. 演示 `@Transactional` 注解失效的 5 种常见场景，并给出解决方案。
177. 模拟金融转账场景：深度对比 `REQUIRED`, `REQUIRES_NEW`, `NESTED` 三种事务传播行为在抛出异常时的回滚范围。
178. 编程式事务实战：使用 `TransactionTemplate` 在一段长逻辑中只对核心数据库操作包裹事务，缩小锁粒度。
179. MySQL InnoDB 的 ACID 特性是如何通过 Redo Log, Undo Log 保证的？
180. 详述 MVCC（多版本并发控制）原理，`Read View` 是如何解决不可重复读的？
181. 动手复现「脏读」「不可重复读」「幻读」三种并发问题：编写多线程测试用例在不同隔离级别下验证数据一致性差异。
182. 深入 MySQL 四种事务隔离级别的实现：READ-UNCOMMITTED、READ-COMMITTED、REPEATABLE-READ、SERIALIZABLE 各是如何通过锁和 MVCC 协同工作的？
183. 分析 Spring 事务管理器的 `rollbackFor` 和 `noRollbackFor`：为什么默认只回滚 RuntimeException 和 Error？
184. 探究 `TransactionSynchronizationManager`：事务同步管理器如何将 Connection 绑定到当前线程，保证多次 DAO 操作共用同一数据库连接？
185. 理解「事务挂起」的内部机制：`REQUIRES_NEW` 是如何保存和恢复当前事务上下文的？
186. 探究 `@Transactional` 在多线程环境下的传播失效：在新线程中通过 `@Async` 调用带有事务注解的方法，事务会传播吗？
187. 编写事务回滚测试：验证在 `REQUIRES_NEW` 传播级别下，内部事务回滚是否会影响外部事务的提交？
188. 模拟事务超时场景：配置 `@Transactional(timeout = 5)`，编写执行超过 5 秒的 SQL，观察事务回滚行为。
189. 深入 Redo Log 机制：Redo Log 的写入时机（WAL 预写日志），以及 Crash Recovery 过程中如何利用 Redo Log 重做数据。
190. 深入 Undo Log 机制：Undo Log 记录了什么信息？事务回滚时如何通过 Undo Log 恢复到事务开始前的状态？
191. 探究 MySQL 的 `autocommit` 与显式事务的区别：为什么在 Spring 中即使不写 `@Transactional` 每条 SQL 也是在事务中执行的？
192. 分析长事务的危害：长事务对 Undo Log 膨胀、锁持有时间、主从延迟的影响。如何在应用层监控和终止长事务？
193. 掌握 `PROPAGATION_MANDATORY` 和 `PROPAGATION_NEVER` 的使用场景：强制要求在事务中执行或强制要求在非事务中执行。
194. 了解 `PROPAGATION_NOT_SUPPORTED` 的行为：在非事务上下文中执行，当前事务被挂起直到方法结束。
195. 探究 Spring 事务 AOP 的 `order` 属性：当自定义 AOP 切面与事务 AOP 共存时，如何通过 `@Order` 控制执行顺序？
196. 模拟事务死锁：开启两个并发事务执行为互相等待对方锁资源的 SQL，查看死锁日志并分析。
197. 理解 InnoDB 事务的隐式提交：哪些 DDL / DCL 语句会触发事务自动提交？
198. 探究 MySQL 8.0 引入的事务特性：`NOWAIT` 和 `SKIP LOCKED` 在 `SELECT ... FOR UPDATE` 中的使用场景。
199. 分析 Spring `@Transactional` 在同一个类中方法调用失效的原因：从 AOP 代理原理角度解释并给出 3 种解决方案。
200. 综合实战：构建一个「秒杀扣库存」场景——在高并发下通过事务、锁、幂等性设计保证库存扣减的原子性和一致性。

**索引优化与锁机制 (201-225)**
201. 构建一张千万级测试表，使用 `EXPLAIN` 分析不同 SQL 的执行计划，解释 `type` 字段（ref, range, index, ALL）的含义。
202. 演示「最左前缀法则」失效的场景（如跳过首列、使用范围查询、字段类型隐式转换）。
203. 什么是聚簇索引与非聚簇索引？演示触发「回表」查询与「索引覆盖」的实际 SQL。
204. 深度锁分析：在可重复读（RR）隔离级别下，开启两个事务，演示 `Next-Key Lock`（间隙锁+行锁）如何阻止幻读。
205. 模拟死锁场景：事务 A 锁行 1 等待行 2，事务 B 锁行 2 等待行 1。分析 MySQL 死锁日志并给出业务层避免死锁的方案（如固定资源申请顺序）。
206. 使用「延迟关联」优化深分页：在 `LIMIT 100000, 20` 场景下，先用子查询获取主键 ID 再关联原表取出完整数据。
207. 对比 `FOR UPDATE`（悲观锁）与 `version` 字段 CAS（乐观锁）在并发扣库存场景下的吞吐量差异。
208. 乐观锁实战：在多线程下使用 `version` 字段 + 自旋重试机制处理并发更新冲突。
209. 悲观锁实战：使用 `SELECT ... FOR UPDATE` 锁定某行，直到事务提交才释放，防止并发脏写。
210. 探究乐观锁的 ABA 问题在高频更新场景下的影响，以及如何通过时间戳版本号加以缓解。
211. 分析 `间隙锁 (Gap Lock)` 在非唯一索引下的加锁范围：为什么间隙锁要阻止 INSERT 而非阻止 SELECT？
212. 探究 MySQL 意向锁（Intention Lock）：IS 锁和 IX 锁在表级锁与行级锁共存时的协调机制。
213. 理解 `LOCK IN SHARE MODE` 与 `FOR UPDATE` 的区别：共享读锁 vs 排他写锁。
214. 模拟「死锁检测与回滚」：开启 `innodb_deadlock_detect`，在死锁发生时 MySQL 如何选择回滚哪个事务？
215. 探究索引下推（Index Condition Pushdown, ICP）：在联合索引查询中，MySQL 如何将过滤条件下推到存储引擎层减少回表？
216. 分析 MRR（Multi-Range Read）：在有索引的范围查询中，MRR 优化如何将随机 IO 转换为顺序 IO？
217. 对比 B+Tree 和 Hash 索引：B+Tree 为什么更适合范围查询？MySQL 中哪些存储引擎支持 Hash 索引？
218. 探究联合索引的「排序」优化：`ORDER BY a, b` 如何利用 `INDEX(a, b)` 避免 filesort？
219. 分析 `filesort` 的两种排序算法：单路排序与双路排序，`max_length_for_sort_data` 参数如何控制排序策略切换？
220. 深入 `EXPLAIN` 的输出：`key_len`、`rows`、`filtered`、`Extra` 字段的含义及在 SQL 调优中的指导意义。
221. 探究前缀索引的创建与优化：对长 VARCHAR 列（如 URL、标题）创建指定前缀长度的索引，权衡选择性和存储空间。
222. 了解全文索引（Fulltext Index）在 MySQL 中的使用，以及与 Elasticsearch 进行全文检索的优劣对比。
223. 掌握覆盖索引的设计思路：通过多余字段构建辅助索引，将查询所需字段全部包含在索引中。
224. 探究 MySQL 8.0 的降序索引和不可见索引特性。
225. 综合实战：给定某电商数据库的慢查询日志，分析 3 个典型的慢 SQL 问题，分别用索引优化、SQL 改写、分表拆表等策略解决。

---

### **模块五：Redis 中间件与缓存架构 (75题)**

**核心知识点**: 数据结构应用、缓存雪崩/击穿/穿透、分布式锁。

**核心数据结构与实战 (226-250)**
226. String：实现针对用户登录的限制，如「连续密码错误 5 次锁定 1 小时」（结合 `INCR` 与 `EXPIRE`）。
227. Hash：设计一个购物车数据结构，实现商品数量的增减。
228. List：实现一个简单的异步任务消息队列，并理解 `BRPOP` 的阻塞读取。
229. Set：实现社交应用中的「共同好友」计算（交集）。
230. ZSet：实现一个带权重的实时排行榜，要求支持分数更新和排名查询。
231. Bitmap：使用 Redis Bitmap 实现用户年度的签到统计，并计算连续签到的最大天数。
232. HyperLogLog：使用 HyperLogLog 实现亿级 UV 的去重统计，理解其 0.81% 误差率的底层原理。
233. Geo：使用 Redis Geo 实现「查找附近的人」，支持根据经纬度搜索周边指定半径内的用户并按距离排序。
234. Stream：使用 Redis Stream（5.0+）实现一个支持消费者组和消息确认的可靠消息队列。
235. 使用 `SCAN` 系列命令安全地遍历海量 Key，对比 `KEYS` 命令的阻塞风险与 SCAN 游标遍历的取舍。
236. 深度理解 Redis 的字符串 SDS（Simple Dynamic String）：为什么 SDS 比 C 字符串更适合用作缓存存储？（二进制安全、O(1) 长度、预分配）。
237. 对比 `String` 和 `Hash` 存储对象：在存储用户信息时，用 JSON 字符串还是 Hash 字段？从内存占用、更新灵活性角度分析。
238. 使用 `EXPIRE` 和 `TTL` 实现带过期时间的缓存，并通过 `PTTL` 精确到毫秒级别的过期检查。
239. 探究 `SETNX`（SET if Not eXists）的原子性与应用场景：分布式锁、防重复提交。
240. 结合 `Lua` 脚本实现复杂的原子性操作：如按条件更新、扣减库存（库存校验 + 扣减一步完成）。
241. 使用 `Pipeline` 批量执行 Redis 命令，减少 RTT（往返时延）开销。对比普通执行和 Pipeline 的耗时差异。
242. 利用 `SINTERSTORE` 和 `SUNIONSTORE` 等集合运算命令实现复杂的标签筛选逻辑。
243. 探究 `ZSet` 的底层结构：ziplist 和 skiplist + dict 的切换条件与性能取舍。
244. 利用 `ZRANK`、`ZREVRANK`、`ZRANGE` 和 `ZCOUNT` 实现榜单的多种查询需求（排名、区间、分数区间）。
245. 使用 `MSET` / `MGET` 批量设值和取值，对比循环逐条操作，提升吞吐量。
246. 探究 Redis Key 的过期策略：惰性删除 + 定期删除如何配合工作？为什么 Redis 不使用定时器精确清理？
247. 使用 `INCRBYFLOAT` 处理浮点型的计数累加，如交易金额金额的实时汇总。
248. 通过 `INFO` 命令监控 Redis 的内存使用、连接数、命中率、命令处理统计等运行指标。
249. 探究 Redis 的 `OBJECT ENCODING` 命令：查看某个 Key 的内部编码（int、embstr、raw、ziplist、linkedlist 等）。
250. 综合实战：使用 Redis 设计「秒杀活动」的数据模型——商品库存（String 扣减）、用户抢购列表（Set 记录）、实时榜单（ZSet 展示最快的 N 个用户）。

**缓存架构与高可用 (251-300)**
251. 编写代码复现「缓存穿透」（查询不存在的数据），并通过布隆过滤器 (Bloom Filter) 予以解决。
252. 模拟「缓存击穿」（热点 Key 过期瞬间），使用双重检查锁（DCL）重建缓存，保护底层数据库。
253. 应对「缓存雪崩」：在写入批量缓存时，为过期时间添加随机抖动值。
254. 保证数据库与缓存的一致性：实战编写「延迟双删」策略，并分析其优缺点。
255. 进阶一致性方案：了解如何通过订阅 MySQL Binlog（如使用 Canal）异步刷新 Redis 缓存。
256. 原理题：Redis 是单线程模型，为什么还能保持极高的吞吐量？（IO 多路复用）。
257. 分布式锁演进：用原生 Redis 实现一个分布式锁，要求包含 `SETNX`、过期时间设定、以及 Lua 脚本保证释放锁的原子性。
258. 引入 Redisson 框架，使用 `RLock`，验证其「看门狗 (Watchdog)」机制是如何自动为持有锁的线程续期的。
259. 深入 RDB 持久化：`SAVE` 与 `BGSAVE` 的区别，`bgsave` 执行期间的写命令是如何处理的（COW 写时复制）。
260. 深入 AOF 持久化：AOF 重写（Rewrite）的原理——如何将增量 AOF 指令压缩为等价的 SET 命令快照？
261. 对比 RDB 与 AOF 的恢复速度、数据安全性、文件大小：在不同业务场景下如何抉择持久化策略？混合持久化的优势是什么？
262. 探究 Redis 的内存淘汰策略：`noeviction`、`allkeys-lru`、`allkeys-lfu`、`volatile-lru`、`volatile-lfu`、`volatile-ttl`、`volatile-random`、`allkeys-random` 的适用场景。
263. 深入 Redis 的内存模型：分析 String 和 Hash 类型分别存储 100 万条用户数据的实际内存占用。
264. 手写 LRU 和 LFU 算法的简化实现，并对比 Redis 近似 LRU/LFU 与精确实现的性能取舍。
265. 探究 Redis Cluster 的分片原理：16384 个 Hash Slot 是如何分配到不同节点的？`CRC16` 算法的计算过程。
266. 了解 Redis Cluster 的客户端重定向：MOVED 与 ASK 错误的区别及 Jedis/Lettuce 中自动重定向的实现。
267. 掌握 Redis Cluster 的高可用：主从复制 + 自动故障转移（Gossip 协议选举新 Master）。
268. 了解 Redis Sentinel 的工作流程：主观下线（SDOWN）和客观下线（ODOWN）的判断过程。
269. 探究 Redis 主从复制机制：全量同步（SYNC / PSYNC）和增量同步的区别，复制积压缓冲区的作用。
270. 分析 Redis 主从复制中的数据延迟与一致性风险：`REPLCONF ACK` 和 `min-slaves-to-write` 参数如何降低数据丢失风险？
271. 探究 Redis 6.0 引入的多线程 IO：主线程仍是单线程执行命令，多线程只负责网络 IO 读写。设计基准测试验证提升效果。
272. 了解 Redis 6.0+ 的 ACL（访问控制列表）：为不同用户配置不同的命令权限和 Key 访问范围。
273. 使用 `CLIENT PAUSE` 命令了解 Redis 的客户端暂停功能在批量操作或在线迁移中的应用。
274. 深入 Redis 事务：`MULTI` / `EXEC` / `WATCH` / `DISCARD` 的使用及 CAS 乐观锁的实现。
275. 对比 Redis 事务与数据库事务的差异：Redis 事务不支持回滚，为什么不支持？设计上如何通过补偿逻辑替代？
276. 探究 Redis Pub/Sub 的优缺点：实时性强但消息不持久。与 Redis Stream 对比其丢消息风险。
277. 了解 `RedisJSON`、`RediSearch`、`RedisTimeSeries`、`RedisGraph` 等模块扩展的能力边界。
278. 使用 Redis 的 `BITFIELD` 命令实现紧凑的整数数组存储，如用户每日步数的月度记录。
279. 探究 Redis `SORT` 命令的能力：结合 `BY`、`LIMIT`、`GET`、`STORE` 实现复杂的数据排序与重组。
280. 编写 Redis 的 `MONITOR` 命令实时监控命令执行流，并控制其在生产环境中使用的风险。
281. 探究客户端连接管理：`maxclients`、`timeout`、`tcp-keepalive` 参数对连接泄露和僵尸连接的影响。
282. 通过 `SLOWLOG` 命令监控 Redis 慢命令，分析耗时原因并优化。
283. 探究 Redis 的 IO 多路复用实现：epoll / kqueue / select 在不同操作系统下的选择与性能差异。
284. 掌握 Redis 的大 Key 和热 Key 排查：使用 `redis-cli --bigkeys` 和 `MEMORY USAGE` 定位异常 Key 并拆分。
285. 对比 Redis Cluster 和 Codis 的架构设计：中心化代理 vs 去中心化 Gossip，各自适用的业务规模。
286. 探究 Redis 7.0+ 引入的 Redis Functions：对比 Lua Script 的改进，函数库的持久化管理。
287. 对比 Lettuce 和 Jedis 客户端的设计理念：同步 vs 异步、连接池 vs 单连接复用。
288. 探究 Spring Cache 的抽象：`@Cacheable`、`@CacheEvict`、`@CachePut` 注解与 Redis 集成的最佳实践。
289. 如何使用 Spring Cache 的多级缓存策略：结合 Caffeine（JVM 本地缓存）和 Redis（远程分布式缓存）。
290. 编写 Lettuce 客户端的连接池配置并进行压力测试，对比 Jedis 的吞吐量差异。
291. 探究 Redis 的 `MEMORY DOCTOR` 命令：自动诊断内存使用问题并给出优化建议。
292. 如何处理缓存热点？通过本地缓存、读写分离副本、随机过期等方式分摊单个 Key 的读写压力。
293. 探究 Redis 的异步删除机制：`UNLINK` 命令和 `lazyfree-lazy-eviction` 参数如何异步释放大 Key 内存，避免主线程阻塞。
294. 了解 Redis 的 CPU 亲和性设置与 NUMA 架构下的性能优化策略。
295. 实现 Redis 的优雅关闭：通过 `SHUTDOWN SAVE` 在停机前触发一次 RDB 持久化。
296. 了解不同 Redis 部署架构的成本与性能：单机、主从、哨兵、Cluster、Proxy 的对比选型矩阵。
297. 模拟 Redis 缓存故障时的降级策略：缓存不可用时如何通过本地缓存或数据库直查兜底，防止服务雪崩。
298. 探究 Redis 的 RDB 文件结构：二进制格式解析，以及通过 `redis-check-rdb` 工具检查持久化文件完整性。
299. 了解 Redis 键空间的订阅通知：`notify-keyspace-events` 配置及其在过期回调中的应用。
300. 综合实战：设计「双十一大促」缓存架构——包含多级缓存（本地+Redis）、缓存预热、热点探测、自动降级、数据兜底、大 Key 拆分的完整方案。

---

### **模块六：消息队列（以 RabbitMQ / Kafka 为例） (50题)**

**核心知识点**: 异步解耦、削峰填谷、消息防丢与幂等性。

301. 在 Spring Boot 中集成 RabbitMQ，声明 Direct, Topic, Fanout 三种类型的交换机并验证路由规则。
302. 设计订单超时未支付自动取消功能：使用 RabbitMQ 的死信队列（DLX）或延迟插件实现延迟消息。
303. 核心问题：如何保证消息**100% 投递成功**？实现生产者确认模式（Confirm Callback）和消息本地表重试机制。
304. 核心问题：如何防止消息被**重复消费**？在消费者端利用数据库唯一索引或 Redis 设计消费幂等性校验。
305. 核心问题：如何保证消息的**顺序消费**？（如 Kafka 单 Partition 或 RabbitMQ 单一队列单消费者方案）。
306. 模拟「削峰填谷」：编写压测脚本瞬间产生万级请求，观察 MQ 堆积情况，并增加消费者节点水平扩展处理能力。
307. 深入 Kafka 的零拷贝（Zero Copy）技术：`sendfile` 系统调用如何将磁盘文件数据直接传输到网络 Socket，绕过用户态内存拷贝？
308. 理解 Kafka 的页缓存（Page Cache）机制：为什么 Kafka 读写主要依赖操作系统的页缓存而非 JVM 堆内存？
309. 探究 Kafka 的副本同步机制：Leader 副本、Follower 副本的角色与同步流程（ISR 列表）。
310. 深入 ISR（In-Sync Replicas）列表管理：Follower 的 Leo 落后 Leader 多久会被踢出 ISR？`replica.lag.time.max.ms` 的作用。
311. 探究 Kafka 的 HW（High Watermark）与 LEO（Log End Offset）：消费者最多能消费到哪条消息？为什么？
312. 理解 Kafka 消费组（Consumer Group）的偏移量管理：`__consumer_offsets` 内部 Topic 的存储结构与提交机制。
313. 探究 Kafka 消费组 Rebalance 机制：什么情况下会触发 Rebalance？如何通过 `session.timeout.ms` 和 `max.poll.interval.ms` 避免频繁重平衡？
314. 分析 Kafka 的 Exactly-Once 语义（EOS）：幂等生产者 + 事务 + 消费者配合机制如何实现精确一次？
315. 探究 Kafka 的分区分配策略：Range、RoundRobin、Sticky、CooperativeSticky 的区别与适用场景。
316. 深入 Kafka Controller 的选举与职责：Controller 如何管理分区 Leader 选举、ISR 变更、Broker 上下线？
317. 介绍 Kafka 的日志存储结构：`.log`、`.index`、`.timeindex` 文件的组织方式，以及日志段（Log Segment）的滚动策略。
318. 理解 Kafka 的日志清理策略：基于时间或大小的删除（`delete`）与基于 Key 的压缩（`compact`）的区别。
319. 探究 Kafka 生产者发送流程：拦截器 → 序列化器 → 分区器 → 缓冲区 → Sender 线程异步批量发送。
320. 分析生产者 `acks` 参数：`acks=0`、`acks=1`、`acks=all(-1)` 对吞吐量和可靠性的影响。
321. 探究生产者 `linger.ms` 和 `batch.size`：如何通过调整这两个参数提升吞吐量并降低网络带宽开销？
322. 理解 `min.insync.replicas`：在 `acks=all` 时与 ISR 最小副本数联动，确保写入的持久性。
323. 深入了解 Kafka Broker 的 `unclean.leader.election.enable` 参数：允许非 ISR 副本当选 Leader 时的数据丢失风险。
324. 介绍 Kafka 的幂等生产者（Idempotent Producer）：`enable.idempotence=true` 如何通过 Producer ID + Sequence Number 去重？
325. 了解 Kafka 的事务生产者与消费者：如何在跨 Topic 跨 Partition 场景下实现原子写入？
326. 探究 Kafka Connect：如何通过 Source Connector 和 Sink Connector 实现异构数据源的流式同步？
327. 了解 Kafka Streams 的流处理抽象：KStream、KTable、GlobalKTable 的区别与应用。
328. 对比 RabbitMQ 与 Kafka 的核心设计哲学：智能 Broker + 简单消费者 vs 简单 Broker + 智能消费者？各自的适用场景。
329. 深入 RabbitMQ 的消息确认机制：自动 ACK 与手动 ACK（`basicAck` / `basicNack` / `basicReject`）的区别与风险。
330. 理解 RabbitMQ 的 `prefetch` 参数（QoS）：如何限制推送给消费者的未确认消息数量，实现公平调度？
331. 对比 RabbitMQ 的持久化消息与 Transient 消息：`deliveryMode = 2`（persistent）对性能的影响有多大？
332. 探究 RabbitMQ 的镜像队列（Mirrored Queue / Quorum Queue）：主从复制与自动故障转移的实现机制。
333. 掌握 RabbitMQ 的内存与磁盘告警机制：高水位线（High Watermark）触发时 RabbitMQ 如何阻塞生产者？
334. 分析 RabbitMQ 的 `mandatory` 标志与 Return Listener：消息不可路由时如何处理退回消息？
335. 实现消息的 TTL（Time-To-Live）：队列级别与消息级别的过期设置，配合死信队列处理超时未消费的消息。
336. 探究 RabbitMQ 的发送方确认模式（Publisher Confirm）：开启 Confirm 后，Broker 何时返回 ACK / NACK？
337. 对比 RabbitMQ Topic 交换机中 `#` 和 `*` 通配符的路由匹配规则并编写测试用例验证。
338. 了解 RabbitMQ 的 Quorum Queue（3.8+）在一致性、故障恢复、延迟方面与经典镜像队列的对比。
339. 实现一个「秒杀活动」的异步下单方案：网关限流 → MQ 削峰 → 订单消费者异步创建 → 结果回调通知。
340. 实现消息的「死信处理」可视化：记录所有进入死信队列的消息，并提供管理后台的查看与重投功能。
341. 编写 Spring Boot + Kafka 的生产者和消费者代码，配置批量消费和手动提交偏移量。
342. 探究 Kafka 消费者的 `enable.auto.commit` 和 `auto.commit.interval.ms`：自动提交偏移量可能带来怎样的重复消费？
343. 探究 Kafka Broker 的 `message.max.bytes`、`replica.fetch.max.bytes`、`fetch.message.max.bytes` 对大消息的支持。
344. 探究消息队列中的「事务消息」实现原理：事务消息的两阶段提交与回查机制（过半确认）。
345. 实现基于消息队列的「可靠事件通知」方案：将数据库操作和消息发送放在同一本地事务中以保证最终一致性。
346. 对比消息队列的推模式（Push）与拉模式（Pull）：长轮询如何在两者之间做折中？
347. 分析 MQ 消息积压的常见原因：消费者慢、下游慢、处理异常重试风暴。给出不同场景的应对方案。
348. 实现消费者的优雅关闭：确保应用退出前将当前正在处理的消息完成后再停止消费。
349. 编写 Kafka MirrorMaker 跨数据中心数据同步方案，解决异地多活的灾备需求。
350. 综合实战：设计一套「可靠消息中心」微服务——消息预存储 → 保证投递（本地消息表 + 定时补偿） → 本地事务协同 → 幂等消费 → 异常预警的完整链路。

---

### **模块七：微服务、高并发与企业级系统设计 (50题)**

**核心知识点**: 金融级架构思维、服务治理、分布式事务。

351. **银行核心场景设计**：设计一个高并发的跨行转账接口，需要考虑账户锁、重试幂等、以及状态流转。
352. 分布式事务实战：使用 Seata 的 AT 模式，实现跨越订单服务、库存服务和账户服务的强一致性事务。
353. 最终一致性实战：使用 RocketMQ 的事务消息机制，实现可靠的异步扣减积分操作。
354. 服务降级与熔断：集成 Sentinel，模拟当下游系统响应超时时，迅速触发熔断，返回友好的降级托底数据。
355. 接口防刷限流：使用 Redis + Lua 脚本实现滑动窗口限流算法，限制单个 IP 或用户的 QPS。
356. 安全防御：在微服务网关（Spring Cloud Gateway）层面，配置统一的 JWT 鉴权与参数验签。
357. 分布式 ID 生成：探究雪花算法 (Snowflake) 的位运算原理，并在本地实现防止时钟回拨的优化逻辑。
358. 设计日终对账系统：银行核心系统与外围支付渠道的「T+1 对账文件」生成、逐笔勾兑、差账处理方案。
359. 深入 ShardingSphere（Sharding-JDBC）：配置水平分库分表规则，并测试跨库 Join 和分页查询的局限性及解决方案。
360. 探究 ShardingSphere 的读写分离机制：配置一主两从，验证事务中的读操作是否一定走主库。
361. 掌握 ShardingSphere 的分布式主键策略：通过雪花算法生成全局唯一 ID，配置在分表中使用。
362. 设计灰度发布方案：基于 Spring Cloud Gateway 的 `WeightRoute` 或者 Nacos 标签路由，按用户 ID 比例将流量导向新版本服务。
363. 实现全链路灰度：流量入口染色 → 网关路由透传 → 微服务间调用保持标签传播 → 日志隔离的全流程。
364. 设计蓝绿部署方案：在 Kubernetes 中运行两套完整环境，通过 Service Selector 标签切换一键完成版本发布与回滚。
365. 探究 Seata TCC 模式与 AT 模式的区别：TCC 的 Try-Confirm-Cancel 三个阶段如何手动控制资源预留和释放？
366. 实现分布式事务的 Saga 模式（Seata Saga）：将长事务分解为多个本地事务，每个本地事务有对应的补偿操作。
367. 对比 2PC、TCC、Saga、可靠消息最终一致 四种分布式事务方案的优缺点及适用场景。
368. 探究 Spring Cloud Alibaba 的服务注册发现：Nacos 如何通过 AP 协议（Distro）实现服务的高可用注册？
369. 探究 Nacos 配置中心实现动态刷新的机制：`@RefreshScope` + `RefreshAutoConfiguration` 的原理。
370. 实现 OpenFeign 的远程调用拦截器：在请求头中透传链路追踪 TraceId 和鉴权 Token。
371. 深入 Spring Cloud LoadBalancer：`@LoadBalancerClient` 的服务列表刷新机制，对比 Ribbon 的差异。
372. 使用 Resilience4j 替换 Sentinel，实现舱壁隔离（Bulkhead）与限时器（TimeLimiter）。
373. 探究限流算法的演进：固定窗口 → 滑动窗口 → 漏桶算法 → 令牌桶算法，各自的特点与适用场景。
374. 深入分布式 ID 生成方案：UUID、数据库自增、号段模式（Leaf-Segment）、雪花算法各自的优劣对比。
375. 探究美团 Leaf 的设计思路：Segment 模式的「双 Buffer」预加载机制，如何实现低延迟 ID 生成？
376. 了解 DDD（领域驱动设计）在微服务拆分中的实践：如何通过限界上下文和聚合根指导服务边界划分？
377. 探究微服务的 API 版本管理策略：URL 版本、Header 版本、Query 参数版本各自的优劣及兼容方案。
378. 实现服务端主动推送方案：对比 WebSocket、SSE（Server-Sent Events）、MQTT 的适用场景。
379. 了解服务网格（Service Mesh）概念：Sidecar 模式（如 Istio）如何将服务治理能力下沉到基础设施层？
380. 实现一个配置推送的灰度功能：使用 Apollo 或 Nacos 的标签灰度，仅将配置推送给打有特定标签的实例。
381. 深入 Sentinel 的滑动窗口统计原理：如何利用 LeapArray 结构高效存储 QPS 统计数据并支持窗口滑动？
382. 探究 Sentinel 的「热点参数限流」：对「某个商品的 ID」级别的精细化流量控制。
383. 实现微服务网关的「统一鉴权中心」：集成 Spring Security + OAuth2，网关承担用户认证与 Token 校验职责。
384. 探究微服务网关的动态路由：基于 Nacos 配置变更实时刷新网关路由规则而无需重启网关实例。
385. 对比 Spring Cloud Gateway 和 Zuul 2.x：Reactor 非阻塞模型（WebFlux）相比 Servlet 阻塞模型的吞吐量优势。
386. 设计「开放平台」的 API 签名认证机制：AppKey + AppSecret + 时间戳 + Nonce + HMAC-SHA256 签名。
387. 实现微服务间的链路追踪：集成 SkyWalking 或 Jaeger，通过 `TraceId` 串联一条完整调用链。
388. 探究链路追踪中 `Trace`、`Span`、`SpanContext` 的概念以及采样策略（全量采样、自适应采样）。
389. 设计统一配置管理系统：配置的分级管理（全局 → 应用 → 集群 → 实例）、加密存储敏感配置、变更审计日志。
390. 探究 Kubernetes 中 Spring Boot 应用的优雅启停：Readiness Probe、Liveness Probe、`preStop` Hook 的配置。
391. 了解 12-Factor App 方法论在 Spring Cloud 微服务中的应用。
392. 设计「秒杀」系统架构：前端限流 → 网关鉴权 → 服务层 Redis + Lua 扣库存 → MQ 异步下单 → 最终一致性的完整链路。
393. 设计「秒杀」中的 URL 动态化防刷机制：每次生成唯一的秒杀链接并在 Redis 中校验。
394. 探究「热点账户」问题：在高频转账中，如何通过账户拆分、异步汇总等方式分散热点账户的写入压力？
395. 设计「分布式定时任务」方案：使用 XXL-JOB 或 Elastic-Job，实现分片广播和故障转移。
396. 探究 XXL-JOB 的调度中心与执行器通信机制：路由策略（轮询、故障转移、分片广播）、任务超时与失败重试。
397. 了解分布式调度框架的「幂等」与「防重」设计：为什么调度中心可能重复触发任务？执行器如何防御？
398. 探究 CAP 定理在微服务中的体现：Nacos（AP）、Consul（CP）、Eureka（AP）各自的一致性选择理由。
399. 了解 Base 理论与柔性事务：与其追求强一致性，如何在「基本可用」和「最终一致」之间做权衡？
400. 综合实战：设计一个「支付系统」的完整架构——包括渠道网关路由、交易订单状态机、分布式事务对账、差错补偿、资金清算的全流程方案。

---

### **模块八：容器化、部署与网络排查 (50题)**

**核心知识点**: Docker 进阶、WSL2 环境调优、持续集成。

401. 编写一个多阶段构建 (Multi-stage build) 的 `Dockerfile`，将 Spring Boot 源码编译打成最小化的 JRE 镜像。
402. 使用 `docker-compose.yml` 一键编排 Spring Boot 应用及其依赖的 MySQL、Redis 容器，并设置正确的启动顺序 (`depends_on`)。
403. **环境排查实战**：在 WSL 2 环境下启动 Docker 容器，解决容器内服务无法绑定到 Windows 宿主机 localhost 的网络映射问题。
404. 深入 Docker 存储卷：将 MySQL 的数据目录和 Spring Boot 的日志目录正确挂载 (Volume) 到宿主机，验证容器销毁后数据不丢失。
405. Docker 网络桥接：自定义一个 Bridge 网络，使微服务容器之间通过容器名（DNS 解析）直接互相通信，而不是写死 IP。
406. 深入 Nginx 反向代理：配置 `nginx.conf` 实现 upstream 负载均衡，并对比轮询、加权轮询、IP Hash 三种策略。
407. 掌握 Nginx 的 `proxy_pass` 规则：带 `/` 与不带 `/` 的区别，URL 路径的拼接规则。
408. 配置 Nginx 的 HTTPS 证书与 HTTP/2：使用 Let's Encrypt 或自签名证书，开启 HTTP/2 以获得多路复用。
409. 实战 Nginx 限流：通过 `limit_req_zone` 和 `limit_conn_zone` 控制单 IP 的请求频率与并发数。
410. 配置 Nginx 的 Gzip 压缩和静态资源缓存策略（`expires`、`Cache-Control`），优化前端加载速度。
411. 使用 `top` 命令分析系统级 CPU、内存负载，理解 `us`、`sy`、`id`、`wa` 的含义，定位性能瓶颈。
412. 使用 `free -m` 查看内存使用，理解 `available` 与 `free` 的区别，buffer/cache 的含义。
413. 使用 `df -h` 和 `du -sh` 排查磁盘空间占用：定位大文件、清理无用的 Docker 镜像和日志。
414. 使用 `netstat -tunlp` 和 `ss` 命令查看端口占用、TCP 连接状态、进程与端口的映射。
415. 利用 `grep`、`awk`、`sed` 分析 Nginx 的 `access.log`：统计 QPS、P99 响应时间、各状态码占比、Top 10 高频 IP。
416. 利用 `awk` 从 Nginx 日志中提取特定字段（如请求耗时 > 3 秒的 URL），汇总生成慢请求报表。
417. 探究 `TIME_WAIT` 状态过多的原因：`net.ipv4.tcp_tw_reuse` 和 `net.ipv4.tcp_fin_timeout` 的系统调优。
418. 排查端口耗尽问题：大量短连接导致 `TCP` 端口资源被占满，如何通过内核参数和连接池优化解决？
419. 使用 `strace` 追踪进程的系统调用，定位应用启动卡住或异常退出的根因。
420. 使用 `lsof -p <pid>` 查看某个 Java 进程打开的所有文件描述符和 Socket 连接，排查连接泄露。
421. 探究 Docker 的 Overlay2 存储驱动原理：镜像层与容器层的写时复制（Copy-on-Write）机制。
422. 优化 Docker 镜像构建速度：利用 BuildKit 的缓存挂载（`--mount=type=cache`）和并行构建。
423. 探究 Docker 的资源限制：通过 `--cpus`、`--memory`、`--memory-swap` 精确控制容器的计算和内存资源。
424. 深入 Docker 的 `HEALTHCHECK` 指令：为 Spring Boot 容器配置健康检查，配合 `docker-compose` 的 `depends_on` 条件。
425. 探究容器内应用获取到的 CPU 核心数问题：Java 8 在容器中会获取宿主机 CPU 核心数，Java 10+ 如何修正？
426. 掌握 Docker Compose 的 `extends`、`profiles`、覆盖合并机制等高级特性。
427. 编写 GitHub Actions 或 Jenkins Pipeline 配置，实现 Spring Boot 应用从源码构建到 Docker 镜像推送到私有仓库。
428. 探究 CI/CD Pipeline 中的构建缓存策略：Maven/Gradle 本地仓库缓存、Docker 层缓存如何加速构建？
429. 使用 Jenkins 实现「蓝海部署」：构建 → 测试 → 部署新版本 → 健康检查 → 切换流量 → 清理旧版本的全流程。
430. 探究 Docker Swarm 或 Kubernetes 中的滚动更新（Rolling Update）与回滚（Rollback）配置参数。
431. 掌握 Kubernetes Deployment 的核心配置：`replicas`、`strategy`、`resources`、`env`、`volumeMounts` 等字段。
432. 了解 Kubernetes Service 的类型：ClusterIP、NodePort、LoadBalancer、ExternalName 的区别。
433. 编写 Kubernetes Ingress 规则实现基于域名的路由分发到不同的 Spring Boot 微服务。
434. 掌握 Kubernetes ConfigMap 和 Secret：将应用的配置文件和环境变量注入到 Pod 中。
435. 分析 Kubernetes Pod 的生命周期：Init Container → Main Container → Readiness Probe → Liveness Probe → preStop → Terminated。
436. 使用 `kubectl` 常用命令：日志查看、Pod 调试、资源编辑、扩缩容、端口转发、Nodeselector。
437. 探究 Kubernetes 的 HPA（水平自动扩缩容）原理：Metrics Server 如何采集数据，基于 CPU/Memory 指标的动态扩缩。
438. 掌握 Kubernetes 的亲和性（Affinity）和反亲和性（Anti-Affinity）：确保关键服务 Pod 分布在不同的物理节点上。
439. 介绍 Helm Chart 的编写与使用方法：如何将微服务部署描述模板化，实现多环境差异化部署。
440. 配置 Prometheus + Grafana 监控 Kubernetes 集群中的 Spring Boot 应用和中间件。
441. 使用 ELK（Elasticsearch + Logstash + Kibana）或 Loki + Grafana 搭建容器化应用的集中式日志系统。
442. 探究 Docker 的安全最佳实践：非 root 用户运行、只读根文件系统、最小化基础镜像、镜像漏洞扫描。
443. 探究 `docker stats` 命令查看容器实时资源使用率，以及如何集成 cAdvisor + InfluxDB 做容器资源监控。
444. 了解 Docker 的 `docker system prune` 系列命令：定期清理无用的镜像、容器、网络、数据卷，回收磁盘空间。
445. 掌握 `jcmd`、`jps`、`jstat` 等 JDK 自带工具在 Docker 容器中的使用：通过 `docker exec` 进入容器执行 JVM 诊断命令。
446. 探究 WSL 2 中 Docker 的文件系统性能问题：代码放在 `/mnt/c/`（Windows 挂载）与 `~/`（Linux 原生）对 IO 性能的影响。
447. 解决 Docker Desktop 在 Windows 上的内存占用过高问题：配置 `.wslconfig` 限制 WSL 2 最大内存和 CPU 核心数。
448. 探究 Docker 的 `--restart=always` 与 `--restart=on-failure`：容器退出后的重启策略选择。
449. 了解 Docker BuildKit 的高级特性：`RUN --mount=type=secret` 安全注入构建时的敏感信息（如私有仓库凭证）。
450. 综合实战：设计一个 Spring Boot 微服务从开发到上线的完整 CI/CD 流水线——Git Push → Jenkins 构建 → 单元+集成测试 → Docker 镜像构建并推送 → K8s 滚动更新 → 健康检查 → 告警通知的端到端方案。

---

### **模块九与十：生产环境故障排查与 JVM 调优 (50题)**

**核心知识点**: OOM 排查、CPU 飙高排查、垃圾回收。

451. 模拟堆内存溢出 (`OutOfMemoryError: Java heap space`)：编写代码不断生成大对象放入 List。
452. 导出 Dump 文件：配置 JVM 参数 `-XX:+HeapDumpOnOutOfMemoryError`，使用 MAT 或 JProfiler 分析内存泄漏的根本对象。
453. 模拟 CPU 100% 场景：编写一个死循环的业务逻辑。
454. 线程排查定位：在 Linux 环境下，通过 `top -Hp <pid>` 找到占用 CPU 最高的线程，将其转换为 16 进制，并在 `jstack` 导出的线程快照中定位到具体代码行。
455. 熟悉 G1 垃圾回收器的工作流程，通过 GC 日志分析 `Mixed GC` 的耗时与停顿原因。
456. 利用 Arthas 诊断工具：在线上运行的 Spring Boot 进程中，动态 `watch` 某个接口的入参和返回值，且无需重启服务。
457. 利用 Arthas 的 `trace` 命令，排查某个接口调用链路中到底哪一层方法耗时最长。
458. 排查类加载器死锁：两个线程各自持有一个类加载锁并等待对方持有的另一个类加载锁，如何通过线程 Dump 定位？
459. 分析 `RejectedExecutionException`：线程池核心线程数、最大线程数、队列容量的配置不合理导致任务被拒绝。给出业务场景的合理线程池参数配置方案。
460. 使用 Arthas `thread -b` 快速定位死锁线程，分析死锁发生的业务逻辑并修复。
461. 探究 Metaspace OOM (`OutOfMemoryError: Metaspace`)：动态生成大量类（如 CGLIB 代理、`@Configuration` 增强类）导致元空间溢出。
462. 模拟直接内存溢出 (`OutOfMemoryError: Direct buffer memory`)：大量分配 NIO ByteBuffer 后未释放导致堆外内存溢出。
463. 利用 `jmap -histo` 和 `jmap -dump` 导出堆快照，分析实例数量最多的对象和占用内存最高的对象。
464. 使用 MAT 的 Leak Suspects 报告和 Dominator Tree 功能，定位内存泄漏的根源对象和 GC Root 引用路径。
465. 探究频繁 Full GC 的原因：老年代增长过快、元空间不足、System.gc() 显式调用。分别给出排查和解决方案。
466. 对比 CMS 和 G1 垃圾回收器：CMS 的碎片化问题与 G1 的 Region 化设计如何影响长时间运行系统的稳定性？
467. 探究 G1 的 Mixed GC 与 Young GC 的区别：Mixed GC 回收部分老年代 Region 的条件（`G1HeapWastePercent`、`G1MixedGCLiveThresholdPercent`）。
468. 了解 ZGC 和 Shenandoah：低延迟垃圾回收器的核心设计目标与 Color Pointer / Brooks Pointer 的实现原理。
469. 探究 JVM SafePoint 机制：STW 暂停时所有线程必须到达 SafePoint。什么情况下 SafePoint 会变「长」？如何排查？
470. 探究 JIT（Just-In-Time）编译的优化技术：方法内联（Inlining）、逃逸分析（Escape Analysis）、锁消除（Lock Elision）。
471. 分析 JIT 编译的逆优化（Deoptimization）：激进优化遇到未预见的执行路径时，JVM 如何回退到解释执行？
472. 探究 Java 的四种引用类型：强引用、软引用（SoftReference）、弱引用（WeakReference）、虚引用（PhantomReference）及其在缓存和内存管理中的应用。
473. 了解 `ThreadLocal` 的内存泄漏风险：`ThreadLocalMap` 中 Entry 的 Key 是弱引用，Value 是强引用。如何在使用后正确 `remove()`？
474. 排查 `ThreadLocal` 内存泄漏导致的老年代堆积：通过 MAT 追踪 `ThreadLocalMap$Entry` → Value 的引用链。
475. 探究缓存一致性问题：CPU 多级缓存 → MESI 协议 → volatile 关键字的 Store-Load 屏障保障可见性。
476. 分析 `synchronized` 的锁升级全过程：偏向锁 → 轻量级锁（CAS 自旋） → 重量级锁（操作系统的 mutex）。
477. 探究 `ConcurrentHashMap` 的实现：JDK 7 分段锁 vs JDK 8 的 CAS + synchronized（链表/红黑树）。
478. 分析 `LongAdder` 优于 `AtomicLong` 的高并发场景：Cell 数组分散竞争的设计原理。
479. 掌握 JVM 参数调优：上线前的内存分配（`-Xmx`, `-Xms`, `-Xmn`, `-XX:MetaspaceSize`）、GC 选择、GC 日志配置。
480. 探究生成环境「STW 时间过长」的排查思路：从 GC 日志中分析是 Young GC 还是 Full GC 导致的长时间停顿，结合内存分配速率（Allocation Rate）判断原因。
481. 分析「堆外内存」泄漏排查：`NIO`、`Netty`、`JNI` 分配的堆外内存，通过 `pmap -x <pid>` 和 `jcmd <pid> VM.native_memory summary` 追踪。
482. 探究 Java 对象的「伪共享」（False Sharing）：CPU Cache Line 导致的性能退化，以及 `@Contended` 注解的解决方案。
483. 利用 `jstack` 分析线程状态分布：RUNNABLE、BLOCKED、WAITING、TIMED_WAITING 分别代表什么？大量 WAITING 线程是正常的吗？
484. 掌握 JVM 的「服务性代理」调试功能：`-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:5005` 远程调试的配置与安全注意事项。
485. 探究 `log4j` / `logback` 在高并发下成为性能瓶颈的原因：同步日志输出加锁竞争。如何通过异步 Appender（如 `AsyncAppender`）优化？
486. 排查「应用假死」问题：程序不响应但 CPU 不高 → 可能是死锁、网络阻塞、数据库连接池耗尽。给出排查流程图。
487. 了解大对象直接进入老年代的策略：`-XX:PretenureSizeThreshold` 参数及大对象分配对 GC 性能的影响。
488. 探究 G1 的 `Humongous Object`（巨型对象）：单个对象超过 Region 一半大小时的特殊分配与回收方式，可能触发 Full GC。
489. 探究 JDK Flight Recorder（JFR）+ JDK Mission Control（JMC）的组合使用：低开销的生产环境性能采样。
490. 编写 JVM 启动时自动配置 GC 日志的脚本：根据 CPU 核数和内存大小自动计算合理的 GC 线程数和堆大小。
491. 排查线上「偶尔 Full GC」问题：通常在业务低峰时段触发，但耗时较长影响用户体验。分析可能原因及优化方向。
492. 了解「Thin Heap」与「Fat Heap」的选择：小堆（< 4GB）与大堆（> 32GB）对 GC 选择和停顿时间的差异。
493. 探究 AQS（AbstractQueuedSynchronizer）的实现：`ReentrantLock` 的公平锁与非公平锁在 `AQS` 中的实现差异。
494. 探究 `CountDownLatch` 和 `CyclicBarrier` 的区别与应用场景。
495. 了解 Java 的 Unsafe 类与 VarHandle（Java 9+）：CAS 操作的底层 API 演进。
496. 探究「fork/join」框架与 CompletableFuture 的差异：任务分解粒度与异步编排能力的对比。
497. 掌握 Spring Boot 应用的 JVM 默认参数分析：使用 `java -XX:+PrintFlagsFinal` 查看当前生效的所有 JVM 配置。
498. 探究「缓存行填充」（Cache Line Padding）技术：在 Disruptor RingBuffer 中如何通过填充避免伪共享。
499. 了解「off-heap」缓存方案：Ehcache、OHC、Chronicle Map 对比，以及它们的适用场景与吞吐量差异。
500. 综合实战报告：面对一个「线上偶发卡顿」的生产事故——从 CPU、内存、GC、线程、网络五维度系统排查，逐步定位到线程池队列积压 → 数据库连接池耗尽 → 下游慢 SQL 引发的雪崩效应，输出完整的分析报告和优化方案。
