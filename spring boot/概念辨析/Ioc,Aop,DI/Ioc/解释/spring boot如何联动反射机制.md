Spring Boot 本身并不直接通过反射进行依赖注入，而是**依赖于 Spring Framework 核心容器**的依赖注入 (DI) 机制。Spring Framework 在实现依赖注入时，确实会**在某些情况下使用反射**。
[[spring boot/概念辨析/Ioc,Aop,DI/Ioc/解释/反射]]
以下是 Spring Framework 如何利用反射进行依赖注入的几种关键方式：

### **1. 构造器注入 (Constructor Injection)**

当 Spring 容器创建一个 bean 时，如果它选择使用构造器注入，它需要实例化这个类。

- **查找构造器**: Spring 会检查类中的构造器。如果有多个构造器，它会根据参数类型和 `@Autowired` 注解（如果存在）来决定使用哪个构造器。
- **实例化**: 一旦确定了构造器，Spring 会使用 `java.lang.reflect.Constructor` 类的 `newInstance()` 方法来创建对象实例。如果构造器有参数，Spring 会先解析这些依赖，然后将它们作为参数传递给 `newInstance()` 方法。

### **2. Setter 方法注入 (Setter Injection)**

如果配置了通过 setter 方法进行注入：

- **查找 Setter 方法**: Spring 会查找符合 JavaBean 规范的 setter 方法（例如，`setDependencyName(...)`）。这通常涉及到扫描类中的方法，并可能通过方法名和参数类型来识别。
- **调用 Setter 方法**: 确定了需要注入的依赖和对应的 setter 方法后，Spring 会使用 `java.lang.reflect.Method` 类的 `invoke()` 方法来调用这个 setter 方法，并将解析好的依赖实例作为参数传入。

### **3. 字段注入 (Field Injection)**

当使用 `@Autowired` 直接注解在字段上时：

- **访问字段**: Spring 需要直接设置私有或保护字段的值。
- **修改可访问性并设值**: 它会使用 `java.lang.reflect.Field` 类。通常，私有字段是不能直接访问的，所以 Spring 会调用 `field.setAccessible(true)` 来绕过 Java 的访问控制检查，然后使用 `field.set(beanInstance, dependencyInstance)` 来设置字段的值。

---

### **核心流程概述**

1. **Bean 定义扫描**: Spring 容器首先会扫描配置（如 XML 文件、Java 配置类中的 `@Bean` 方法，或组件扫描 `@Component`, `@Service`, `@Repository` 等注解）来识别需要管理的 bean 及其依赖关系。
2. **Bean 实例化**: 当需要某个 bean 的实例时，容器会负责创建它。
    - 如果是构造器注入，Spring 会通过反射调用选定的构造器。
3. **依赖解析与注入**:
    - 容器会解析当前 bean 所依赖的其他 bean。
    - 如果依赖尚未创建，容器会递归地创建这些依赖。
    - 一旦依赖的 bean 实例可用：
        - 对于 **setter 注入**，Spring 通过反射调用相应的 setter 方法。
        - 对于 **字段注入**，Spring 通过反射直接设置字段的值。
4. **Bean 后处理器 (BeanPostProcessors)**: Spring 提供了如 `AutowiredAnnotationBeanPostProcessor` 这样的后处理器。这些处理器在 bean 初始化生命周期的特定阶段被调用。例如，`AutowiredAnnotationBeanPostProcessor` 会查找带有 `@Autowired` 注解的字段、构造器和方法，并使用反射来注入所需的依赖。

---

### **总结**

虽然 Spring Boot 简化了 Spring 应用的开发和配置，但其底层的依赖注入机制仍然由 Spring Framework 提供。Spring Framework 为了灵活性和自动化配置，广泛地在bean的实例化、依赖关系的解析以及将依赖注入到相应bean的字段或方法中时**策略性地使用Java反射机制**。

值得注意的是，过度使用反射可能会带来一些性能开销，但 Spring Framework 对此进行了优化，并且在大多数应用场景下，这种开销是可以接受的，其带来的便利性和解耦性远超其性能影响。现代 Spring 版本也倾向于推荐使用构造器注入，因为它能更好地保证对象的不变性和依赖的明确性。