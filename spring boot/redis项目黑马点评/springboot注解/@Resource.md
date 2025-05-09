`@Resource` 注解是 Java EE (现在是 Jakarta EE) 提供的依赖注入注解，它的作用是：

1. **依赖注入**：自动将匹配的 Bean 注入到标记的字段或方法中
2. **按名称匹配**：默认按变量名称查找匹配的 Bean（与 `@Autowired` 不同，后者默认按类型匹配）
3. **简化资源获取**：不需要手动创建或查找依赖对象

在您的
```java

`MvcConfig`

类中：

Copy

@Resource

private StringRedisTemplate stringRedisTemplate;
```
这行代码的作用是：

- 告诉 Spring 容器自动注入一个 `StringRedisTemplate` 类型的 Bean
- 注入的 Bean 名称应该与字段名 `stringRedisTemplate`匹配
- 如果找不到名称匹配的 Bean，则会尝试按类型匹配

`@Resource` 与 `@Autowired` 的主要区别：

- `@Resource` 是 Java 标准注解 (JSR-250)，而 `@Autowired` 是 Spring 特有的
- `@Resource` 默认按名称匹配，而 `@Autowired` 默认按类型匹配
- `@Resource` 可以指定 `name` 属性来明确指定 Bean 名称

在这个特定场景中，`@Resource` 确保了 `StringRedisTemplate` 被正确注入，以便在拦截器中使用 Redis 功能。

