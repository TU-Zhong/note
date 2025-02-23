`ApplicationContext` 是Spring框架中的一个核心接口，它是Spring IoC（Inversion of Control，控制反转）容器的实现之一。它是Spring框架用于管理Bean（即应用程序中的对象）的主要接口，提供了许多强大的功能来支持应用程序的开发。

---

### **什么是 `ApplicationContext`？**
`ApplicationContext` 是Spring框架中的一个高级容器，负责：
1. **实例化、配置和管理Bean**：它根据配置文件或注解创建和管理应用程序中的所有Bean。
	
	- <mark style="background: #FFF3A3A6;">实例化指容器启动时创建具体的Bean对象</mark>
	- <mark style="background: #FFF3A3A6;">配置指容器按照注解或者xml文件或者外部配置文件如.properties和.yml文件来定义Bean对</mark><mark style="background: #FFF3A3A6;">象</mark>
	- <mark style="background: #FFF3A3A6;">管理Bean是指进行依赖注入  ，以及特定Bean创建时调用初始化方法和销毁方法</mark>
2. **依赖注入（DI）**：它自动解决Bean之间的依赖关系，将所需的依赖注入到Bean中。
3. **提供应用程序的运行时环境**：它封装了应用程序的配置、资源加载、事件发布等功能。
	这个看这个的手动上下文配置 .yml文件,也就是属性源那些东西
	[[数据源 代码]]

`ApplicationContext` 是 `BeanFactory` 接口的扩展，提供了更多企业级功能，比如国际化支持、事件发布、资源加载等。

---

### **`ApplicationContext` 的主要作用**
1. **Bean管理**：
	[[bean管理具体指什么]]
   - 负责创建、配置和管理应用程序中的所有Bean。
   - 通过配置文件（如XML）或注解（如`@Component`、`@Service`、`@Repository`等）来定义Bean。
   - 支持Bean的依赖注入（DI）和生命周期管理（如初始化、销毁）。

3. **依赖注入（DI）**：
   - 自动解决Bean之间的依赖关系，将所需的依赖注入到Bean中。
   - 例如，如果一个`Service`类依赖于一个`Repository`类，Spring会自动将`Repository`实例注入到`Service`中。

4. **配置管理**：
   - 支持多种配置方式，如XML、Java注解、Java配置类等。
   - 可以加载外部配置文件（如`application.properties`或`application.yml`）。

5. **资源加载**：
   - 提供统一的资源加载机制，可以从文件系统、类路径、URL等位置加载资源。
   - 例如，可以通过`context.getResource("classpath:file.txt")`加载类路径下的文件。

6. **国际化支持**：
   - 支持多语言消息的国际化（i18n），可以通过`MessageSource`接口获取本地化的消息。

7. **事件发布与监听**：
   - 支持事件驱动编程，可以发布和监听应用程序事件。
   - 例如，可以通过`context.publishEvent(new MyEvent())`发布自定义事件，并通过`@EventListener`注解监听事件。

8. **AOP（面向切面编程）支持**：
   - 支持AOP，可以通过配置实现日志记录、事务管理、权限检查等横切关注点。

---

### **`ApplicationContext` 的实现类**
Spring提供了多种`ApplicationContext`的实现类，常用的包括：
9. **`AnnotationConfigApplicationContext`**：
   - 基于Java注解配置的上下文，适用于使用`@Configuration`和`@Bean`注解的配置类。
   - 示例：
     ```java
     ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
     ```

10. **`ClassPathXmlApplicationContext`**：
   - 基于XML配置的上下文，适用于传统的XML配置文件。
   - 示例：
     ```java
     ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
     ```

11. **`FileSystemXmlApplicationContext`**：
   - 基于文件系统路径的XML配置上下文，适用于从文件系统加载XML配置文件。
   - 示例：
     ```java
     ApplicationContext context = new FileSystemXmlApplicationContext("C:/config/applicationContext.xml");
     ```

12. **`WebApplicationContext`**：
   - 专为Web应用程序设计的上下文，通常用于Spring MVC或Spring Boot Web应用。

---

### **`ApplicationContext` 的使用示例**
以下是一个简单的示例，展示如何使用`ApplicationContext`获取Bean并调用其方法：
```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        // 启动Spring应用程序并获取ApplicationContext
        ApplicationContext context = SpringApplication.run(MyApp.class, args);

        // 从ApplicationContext中获取Bean
        MyService myService = context.getBean(MyService.class);

        // 调用Bean的方法
        myService.doSomething();
    }
}

@Service
class MyService {
    public void doSomething() {
        System.out.println("Hello from MyService!");
    }
}
```

---

### **`ApplicationContext` 的生命周期**
13. **初始化**：
   - 容器启动时，读取配置文件或注解，创建并配置Bean。
   - 调用Bean的初始化方法（如`@PostConstruct`注解的方法）。

14. **运行**：
   - 容器管理Bean的生命周期，处理依赖注入，提供服务。

15. **销毁**：
   - 容器关闭时，调用Bean的销毁方法（如`@PreDestroy`注解的方法）。

---

### **总结**
`ApplicationContext` 是Spring框架的核心容器，负责管理Bean的生命周期、依赖注入、配置加载、资源管理等功能。它是Spring应用程序的基础，几乎所有的Spring应用都离不开它。通过`ApplicationContext`，开发者可以专注于业务逻辑，而无需手动管理对象的创建和依赖关系。