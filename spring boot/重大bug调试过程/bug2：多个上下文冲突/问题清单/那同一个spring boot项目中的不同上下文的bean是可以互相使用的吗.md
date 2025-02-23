在同一个 Spring Boot 项目中，**不同上下文的 Bean 默认是不能直接互相使用的**，因为每个上下文（`ApplicationContext`）都是独立的，它们管理自己的 Bean 集合。如果想让不同上下文的 Bean 互相使用，需要通过一些额外的手段来实现。

---

### 1. **默认情况下，不同上下文的 Bean 是隔离的**
每个 `ApplicationContext` 都有自己的 Bean 工厂和 Bean 定义，它们之间默认是相互隔离的。例如：

```java
// 上下文 1
AnnotationConfigApplicationContext context1 = new AnnotationConfigApplicationContext();
context1.register(Config1.class);
context1.refresh();

// 上下文 2
AnnotationConfigApplicationContext context2 = new AnnotationConfigApplicationContext();
context2.register(Config2.class);
context2.refresh();

// 上下文 1 中的 Bean 无法直接注入到上下文 2 中
```

---

### 2. **如何让不同上下文的 Bean 互相使用？**

#### 方法 1：**设置父子上下文关系**
通过将一个上下文设置为另一个上下文的父上下文，子上下文可以访问父上下文中的 Bean，但父上下文不能访问子上下文中的 Bean。

##### 示例：
```java
// 父上下文
AnnotationConfigApplicationContext parentContext = new AnnotationConfigApplicationContext();
parentContext.register(ParentConfig.class);
parentContext.refresh();

// 子上下文
AnnotationConfigApplicationContext childContext = new AnnotationConfigApplicationContext();
childContext.setParent(parentContext); // 设置父上下文
childContext.register(ChildConfig.class);
childContext.refresh();

// 子上下文可以访问父上下文中的 Bean
ParentBean parentBean = childContext.getBean(ParentBean.class);
System.out.println(parentBean);

// 父上下文不能访问子上下文中的 Bean
// 以下代码会抛出 NoSuchBeanDefinitionException
ChildBean childBean = parentContext.getBean(ChildBean.class);
```

##### 适用场景：
- 当你希望共享一些公共的 Bean（如数据源、配置类）时。
- 子上下文需要访问父上下文中的 Bean，但父上下文不需要访问子上下文中的 Bean。

---

#### 方法 2：**手动传递 Bean**
将一个上下文中的 Bean 手动传递到另一个上下文中。

##### 示例：
```java
// 上下文 1
AnnotationConfigApplicationContext context1 = new AnnotationConfigApplicationContext();
context1.register(Config1.class);
context1.refresh();

// 上下文 2
AnnotationConfigApplicationContext context2 = new AnnotationConfigApplicationContext();
context2.register(Config2.class);
context2.refresh();

// 手动将上下文 1 中的 Bean 传递到上下文 2 中
MyBean myBean = context1.getBean(MyBean.class);
context2.getBeanFactory().registerSingleton("myBean", myBean);

// 现在上下文 2 可以使用上下文 1 中的 Bean
MyBean beanInContext2 = context2.getBean(MyBean.class);
System.out.println(beanInContext2);
```

##### 适用场景：
- 当你需要临时共享某个 Bean 时。
- 不适合大规模共享 Bean。

---

#### 方法 3：**使用共享的 Bean 定义**
将需要共享的 Bean 定义放在一个公共的配置类中，然后在不同的上下文中导入该配置类。

##### 示例：
```java
// 公共配置类
@Configuration
public class SharedConfig {
    @Bean
    public SharedBean sharedBean() {
        return new SharedBean();
    }
}

// 上下文 1
AnnotationConfigApplicationContext context1 = new AnnotationConfigApplicationContext();
context1.register(SharedConfig.class, Config1.class);
context1.refresh();

// 上下文 2
AnnotationConfigApplicationContext context2 = new AnnotationConfigApplicationContext();
context2.register(SharedConfig.class, Config2.class);
context2.refresh();

// 两个上下文都可以访问 SharedBean
SharedBean sharedBean1 = context1.getBean(SharedBean.class);
SharedBean sharedBean2 = context2.getBean(SharedBean.class);
System.out.println(sharedBean1 == sharedBean2); // 输出 true（单例模式下是同一个 Bean）
```

##### 适用场景：
- 当你需要共享一些公共的 Bean 时。
- 适合大规模共享 Bean。

---

#### 方法 4：**使用 `@Import` 导入配置**
在一个上下文中使用 `@Import` 注解导入另一个上下文的配置类，从而共享 Bean。

##### 示例：
```java
// 配置类 1
@Configuration
public class Config1 {
    @Bean
    public Bean1 bean1() {
        return new Bean1();
    }
}

// 配置类 2
@Configuration
@Import(Config1.class) // 导入配置类 1
public class Config2 {
    @Bean
    public Bean2 bean2() {
        return new Bean2();
    }
}

// 上下文
AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();
context.register(Config2.class);
context.refresh();

// 可以访问 Config1 和 Config2 中的 Bean
Bean1 bean1 = context.getBean(Bean1.class);
Bean2 bean2 = context.getBean(Bean2.class);
System.out.println(bean1);
System.out.println(bean2);
```

##### 适用场景：
- 当你想在一个上下文中复用另一个上下文的配置时。

---

### 3. **Spring Boot 中的上下文**
在 Spring Boot 项目中，默认只有一个 `ApplicationContext`，即 Spring Boot 的主上下文。如果你手动创建了其他上下文，需要显式设置父子关系或手动传递 Bean。

---

### 4. **总结**
在同一个 Spring Boot 项目中，不同上下文的 Bean 默认是隔离的，但可以通过以下方式让它们互相使用：
1. **设置父子上下文关系**：子上下文可以访问父上下文中的 Bean。
2. **手动传递 Bean**：将一个上下文中的 Bean 手动注册到另一个上下文中。
3. **使用共享的 Bean 定义**：将公共的 Bean 定义放在一个配置类中，然后在不同上下文中导入。
4. **使用 `@Import` 导入配置**：在一个上下文中导入另一个上下文的配置类。

选择哪种方式取决于具体的需求和场景。如果你有更具体的需求或问题，可以进一步讨论！