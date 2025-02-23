---

在 Spring 中，`@Bean` 注解用于 **配置类** 中定义 Bean，通常与 `@Configuration` 注解配合使用。通过 `@Bean` 注解定义的 Bean 可以像普通 Bean 一样进行依赖注入。以下是详细的写法和使用场景。

---

### **1. 基本用法**
在配置类中使用 `@Bean` 注解定义一个 Bean，并通过方法参数实现依赖注入。

#### **示例**：
```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService(MyRepository myRepository) {
        return new MyServiceImpl(myRepository);
    }

    @Bean
    public MyRepository myRepository() {
        return new MyRepositoryImpl();
    }
}
```

#### **解释**：
- `myRepository()` 方法定义了一个 `MyRepository` Bean。
- `myService()` 方法定义了一个 `MyService` Bean，并通过参数注入了 `MyRepository`。

---

### **2. 依赖注入的方式**
#### **(1) 方法参数注入**
Spring 会自动将匹配的 Bean 作为参数传递给 `@Bean` 方法。

**示例**：
```java
@Bean
public MyService myService(MyRepository myRepository) {
    return new MyServiceImpl(myRepository);
}
```

#### **(2) 直接调用方法**
在 `@Bean` 方法中直接调用其他 `@Bean` 方法。

**示例**：
```java
@Bean
public MyService myService() {
    return new MyServiceImpl(myRepository());
}

@Bean
public MyRepository myRepository() {
    return new MyRepositoryImpl();
}
```

#### **(3) 字段注入**
在配置类中使用 `@Autowired` 注入依赖，然后在 `@Bean` 方法中使用。

**示例**：
```java
@Configuration
public class AppConfig {

    @Autowired
    private MyRepository myRepository;

    @Bean
    public MyService myService() {
        return new MyServiceImpl(myRepository);
    }

    @Bean
    public MyRepository myRepository() {
        return new MyRepositoryImpl();
    }
}
```

---

### **3. 高级用法**
#### **(1) 指定 Bean 名称**
默认情况下，`@Bean` 方法的名称就是 Bean 的名称。可以通过 `name` 属性指定 Bean 名称。

**示例**：
```java
@Bean(name = "customService")
public MyService myService() {
    return new MyServiceImpl();
}
```

#### **(2) 条件化注册 Bean**
使用 `@Conditional` 注解根据条件动态注册 Bean。

**示例**：
```java
@Bean
@Conditional(MyCondition.class)
public MyService myService() {
    return new MyServiceImpl();
}
```

#### **(3) 生命周期回调**
使用 `initMethod` 和 `destroyMethod` 指定 Bean 的初始化和销毁方法。

**示例**：
```java
@Bean(initMethod = "init", destroyMethod = "cleanup")
public MyService myService() {
    return new MyServiceImpl();
}
```

---

### **4. 最佳实践**
#### **(1) 优先使用方法参数注入**
方法参数注入是官方推荐的方式，因为它更直观且易于测试。

**示例**：
```java
@Bean
public MyService myService(MyRepository myRepository) {
    return new MyServiceImpl(myRepository);
}
```

#### **(2) 避免在 `@Bean` 方法中直接调用其他 `@Bean` 方法**
直接调用其他 `@Bean` 方法会导致每次调用都创建一个新的实例，而不是从容器中获取单例。

**错误示例**：
```java
@Bean
public MyService myService() {
    return new MyServiceImpl(myRepository()); // 每次调用都会创建新的 MyRepository 实例
}

@Bean
public MyRepository myRepository() {
    return new MyRepositoryImpl();
}
```

**正确示例**：
```java
@Bean
public MyService myService(MyRepository myRepository) {
    return new MyServiceImpl(myRepository); // 从容器中获取单例
}

@Bean
public MyRepository myRepository() {
    return new MyRepositoryImpl();
}
```

#### **(3) 使用 `@Configuration` 注解**
确保配置类使用 `@Configuration` 注解，以便 Spring 能够正确处理 `@Bean` 方法。

**示例**：
```java
@Configuration
public class AppConfig {
    @Bean
    public MyService myService() {
        return new MyServiceImpl();
    }
}
```

---

### **5. 示例代码**
以下是一个完整的示例，展示了如何使用 `@Bean` 注解进行依赖注入：

```java
@Configuration
public class AppConfig {

    @Bean
    public MyService myService(MyRepository myRepository) {
        return new MyServiceImpl(myRepository);
    }

    @Bean
    public MyRepository myRepository() {
        return new MyRepositoryImpl();
    }
}

public class MyServiceImpl implements MyService {
    private final MyRepository myRepository;

    public MyServiceImpl(MyRepository myRepository) {
        this.myRepository = myRepository;
    }

    @Override
    public void doSomething() {
        myRepository.save();
    }
}

public class MyRepositoryImpl implements MyRepository {
    @Override
    public void save() {
        System.out.println("Saving data...");
    }
}
```

---

### **6. 总结**
- **`@Bean` 注解用于在配置类中定义 Bean。**
- **依赖注入可以通过方法参数、直接调用方法或字段注入实现。**
- **优先使用方法参数注入，确保 Bean 的单例特性。**
- **使用 `@Configuration` 注解标记配置类。**

通过合理使用 `@Bean` 注解，可以灵活地管理 Spring 容器中的 Bean 及其依赖关系。