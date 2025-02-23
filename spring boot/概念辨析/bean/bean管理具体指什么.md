在Spring框架中，**Bean管理**指的是Spring容器（如`ApplicationContext`）对应用程序中的对象（称为Bean）的创建、配置、依赖注入和生命周期管理。Spring通过配置文件（如XML）或注解（如`@Component`、`@Service`等）来定义Bean，并自动管理这些Bean的实例化和依赖关系。

下面通过具体例子详细解释**Bean管理**的各个方面。

---

### **1. Bean的创建**
Spring容器负责根据配置或注解创建Bean的实例。

#### 示例：
```java
@Service
public class UserService {
    public void greet() {
        System.out.println("Hello from UserService!");
    }
}
```

在上面的代码中：
- `@Service` 注解告诉Spring，`UserService` 是一个Bean，Spring容器会在启动时自动创建它的实例。

---

### **2. Bean的配置**
<mark style="background: #FFF3A3A6;">说白了就是如何定义bean</mark>
Spring支持多种方式配置Bean，包括XML、注解和Java配置类。

#### 示例（基于注解的配置）：
```java
@Configuration
public class AppConfig {
    @Bean
    public UserService userService() {
        return new UserService();
    }
}
```

在上面的代码中：
- `@Configuration` 注解表示这是一个配置类。
- `@Bean` 注解表示 `userService()` 方法返回的对象是一个Bean，Spring会管理它的生命周期。

---

### **3. 依赖注入（DI）**
Spring容器会自动解决Bean之间的依赖关系，并将所需的依赖注入到Bean中。
<mark style="background: #FFF3A3A6;">依赖什么只需要在函数的参数中写出来就可以了</mark>
## 非配置类，普通组件
<mark style="background: #FFF3A3A6;">只能在spring管理的bean中使用，否则会注入失败
有些情况可以使用@Autowired有些可以不用，但是建议所有情况都进行使用，而且推荐构造方法注入（官方推荐），因为它可以确保依赖不可变，并且更容易进行单元测试。</mark>
## 配置类
[[@Bean注解方法的依赖注入]]
#### 示例：
```java
@Service
public class UserService {
    private final UserRepository userRepository;

    @Autowired
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public void saveUser(String name) {
        userRepository.save(name);
    }
}

@Repository
public class UserRepository {
    public void save(String name) {
        System.out.println("Saving user: " + name);
    }
}
```

在上面的代码中：
- `UserService` 依赖于 `UserRepository`。
- `@Autowired` 注解告诉Spring，将 `UserRepository` 的实例注入到 `UserService` 的构造函数中。

---

### **4. Bean的生命周期管理**
Spring容器管理Bean的整个生命周期，包括初始化和销毁。

#### 示例：
```java
@Service
public class UserService {
    @PostConstruct
    public void init() {
        System.out.println("UserService initialized!");
    }

    @PreDestroy
    public void cleanup() {
        System.out.println("UserService destroyed!");
    }
}
```

在上面的代码中：
- `@PostConstruct` 注解的方法会在Bean初始化后调用。
- `@PreDestroy` 注解的方法会在Bean销毁前调用。

---

### **5. 获取Bean**
可以通过`ApplicationContext`获取Spring容器中的Bean。

#### 示例：
```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(MyApp.class, args);

        // 获取UserService Bean
        UserService userService = context.getBean(UserService.class);
        userService.greet();

        // 获取UserRepository Bean
        UserRepository userRepository = context.getBean(UserRepository.class);
        userRepository.save("John Doe");
    }
}
```

在上面的代码中：
- `context.getBean(UserService.class)` 从Spring容器中获取 `UserService` 的实例。
- `context.getBean(UserRepository.class)` 从Spring容器中获取 `UserRepository` 的实例。

---

### **6. Bean的作用域**
Spring支持多种Bean的作用域，常用的包括：
- **Singleton**（默认）：整个Spring容器中只有一个实例。
- **Prototype**：每次请求时都会创建一个新的实例。

#### 示例：
```java
@Service
@Scope("prototype")
public class UserService {
    public void greet() {
        System.out.println("Hello from UserService!");
    }
}
```

在上面的代码中：
- `@Scope("prototype")` 注解表示每次获取 `UserService` 时都会创建一个新的实例。

---

### **总结**
**Bean管理**是Spring框架的核心功能之一，具体包括：
1. **创建Bean**：通过配置文件或注解定义Bean。
2. **配置Bean**：设置Bean的属性和依赖关系。
3. **依赖注入**：自动解决Bean之间的依赖关系。
4. **生命周期管理**：管理Bean的初始化和销毁。
5. **获取Bean**：通过`ApplicationContext`获取Bean的实例。
6. **作用域管理**：控制Bean的生命周期范围（如单例或原型）。

通过这些功能，Spring极大地简化了应用程序的开发和维护，开发者可以专注于业务逻辑，而无需手动管理对象的创建和依赖关系。