在 Spring Boot 中，`#` 是 **SpEL（Spring Expression Language）** 的起始符号。SpEL 是 Spring 提供的一种强大的表达式语言，用于在运行时动态地求值和操作对象。

---

### `#` 的作用
`#` 表示后面的内容是一个 **SpEL 表达式**，Spring 会解析并执行这个表达式。SpEL 支持多种操作，包括：
- 访问对象的属性和方法。
- 调用静态方法或构造函数。
- 数学运算、逻辑运算、条件判断等。
- 集合操作（如列表、数组、Map 等）。

在 `@Value` 注解中，`#` 用于表示要使用 SpEL 来动态处理值，而不是直接注入静态值或配置文件中的值。

---

### 示例解析

#### 1. **动态处理配置值**
```java
@Value("#{'${app.ports}'.split(',')}")
private List<String> portList;
```
- `#` 表示后面的内容是一个 SpEL 表达式。
- `'${app.ports}'` 从配置文件中读取 `app.ports` 的值，并将其作为字符串传递给 SpEL。
- `split(',')` 是 SpEL 中的方法调用，将字符串按逗号分隔。

---

#### 2. **动态计算值**
```java
@Value("#{T(java.lang.Math).random() * 100}")
private double randomNumber;
```
- `#` 表示后面的内容是一个 SpEL 表达式。
- `T(java.lang.Math).random()` 调用 `Math.random()` 方法生成一个随机数。
- `* 100` 将随机数乘以 100。

---

#### 3. **访问 Bean 的属性或方法**
```java
@Value("#{myBean.getProperty()}")
private String myProperty;
```
- `#` 表示后面的内容是一个 SpEL 表达式。
- `myBean.getProperty()` 调用 Spring 容器中名为 `myBean` 的 Bean 的 `getProperty()` 方法。

---

### `#` 和 `$` 的区别
- `#`：用于 SpEL 表达式，支持动态操作和计算。
- `$`：用于属性占位符，表示从配置文件中读取值。

---

### 总结
`#` 是 SpEL 表达式的起始符号，用于动态处理值或执行操作。在 `@Value` 注解中，`#` 可以结合属性占位符（`$`）或直接使用 SpEL 表达式，实现灵活的值注入和动态计算。