

在Spring Boot中，`@RequestParam` <mark style="background: #FFF3A3A6;">注解用于从HTTP请求的查询参数或表单数据中提取值，并将其绑定到控制器方法的参数上。以下是其核心要点：</mark>

### 核心特性
1. **参数绑定**  
   自动将URL查询参数或表单字段值映射到方法参数。
   ```java
   @GetMapping("/hello")
   public String sayHello(@RequestParam String name) {
       return "Hello, " + name; // 访问 /hello?name=Alice → "Hello, Alice"
   }
   ```

2. **常用属性**  
   - `value/name`：指定请求参数的名称（若方法参数名不同）。
   - `required`：是否必传（默认`true`，缺失时抛异常）。
   - `defaultValue`：参数缺失时的默认值（自动将`required`设为`false`）。
   ```java
   @GetMapping("/user")
   public String getUser(@RequestParam(name = "id", required = false, defaultValue = "1") String userId) {
       // 访问 /user → 使用默认值1；/user?id=5 → userId=5
   }
   ```

3. **多参数处理**  
   可单独声明多个参数，或用`Map`接收所有参数：
   ```java
   @GetMapping("/search")
   public String search(@RequestParam String keyword, @RequestParam int page) { ... }

   // 或接收全部参数
   @GetMapping("/filter")
   public String filter(@RequestParam Map<String, String> params) { ... }
   ```

### 对比`@PathVariable`
- **`@RequestParam`**：处理查询参数（如`/path?param=value`）。
- **`@PathVariable`**：处理URL路径中的变量（如`/path/{value}`）。

### 适用场景
- 处理GET请求的查询参数。
- 接收POST表单提交的数据（Content-Type为`application/x-www-form-urlencoded`）。

通过`@RequestParam`，开发者能便捷地获取请求中的动态参数，简化参数处理逻辑。