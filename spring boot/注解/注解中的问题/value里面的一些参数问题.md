在 Spring Boot 中，`@Value` 注解支持使用 **SpEL（Spring Expression Language）** 来动态处理值。`#{'${app.ports}'.split(',')}` 的语法结合了 SpEL 和配置文件注入的特性。下面详细解释为什么需要加引号以及使用 `split` 函数：

---

### 1. **为什么要加引号？**
`'${app.ports}'` 中的单引号是为了将 `${app.ports}` 作为一个字符串传递给 SpEL 表达式。

- `${app.ports}` 是 Spring 的 **属性占位符**，它会从配置文件（如 `application.properties`）中读取 `app.ports` 的值。
- 如果没有单引号，`${app.ports}` 会被 SpEL 直接解析为配置值，而 SpEL 无法直接对配置值调用方法（如 `split`）。
- 加上单引号后，`'${app.ports}'` 表示将 `${app.ports}` 解析后的值作为一个字符串传递给 SpEL，然后 SpEL 可以对这个字符串调用方法。

---

### 2. **为什么要用 `split` 函数？**
`split` 函数的作用是将字符串按照指定的分隔符拆分成一个数组或列表。

- 假设 `application.properties` 中有如下配置：
  ```properties
  app.ports=8080,8081,8082
  ```
- `${app.ports}` 会被解析为字符串 `"8080,8081,8082"`。
- 使用 `split(',')` 将这个字符串按逗号分隔，得到一个数组 `["8080", "8081", "8082"]`。

---

### 3. **完整解析过程**
`#{'${app.ports}'.split(',')}` 的执行步骤如下：
1. **解析属性占位符**：  
   `${app.ports}` 从配置文件中读取值，假设值为 `"8080,8081,8082"`。

2. **转换为字符串**：  
   `'${app.ports}'` 将解析后的值转换为字符串 `"8080,8081,8082"`。

3. **调用 `split` 函数**：  
   `split(',')` 将字符串按逗号分隔，得到数组 `["8080", "8081", "8082"]`。

4. **注入到 `List<String>`**：  
   Spring 会将数组转换为 `List<String>`，并注入到 `portList` 中。

---

### 4. **示例代码**
```java
@Component
public class AppConfig {

    @Value("#{'${app.ports}'.split(',')}")
    private List<String> portList; // 注入列表 ["8080", "8081", "8082"]

    public List<String> getPortList() {
        return portList;
    }
}
```

---

### 5. **总结**
- **加引号**：将 `${app.ports}` 解析后的值作为字符串传递给 SpEL。
- **使用 `split` 函数**：将字符串按逗号分隔，得到数组或列表。
- **最终结果**：将配置文件中的逗号分隔字符串转换为 `List<String>` 并注入到字段中。

---

### 6. **其他注意事项**
- 如果配置文件中没有 `app.ports`，会抛出异常。可以通过设置默认值来避免：
  ```java
  @Value("#{'${app.ports:8080,8081}'.split(',')}")
  private List<String> portList; // 默认值为 ["8080", "8081"]
  ```
- 如果分隔符不是逗号，可以修改 `split` 的参数，例如 `split(';')` 表示按分号分隔。