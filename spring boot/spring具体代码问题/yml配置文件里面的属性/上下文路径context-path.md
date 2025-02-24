在Spring Boot项目中，`servlet.context-path` 是用于配置应用程序的 **上下文路径（Context Path）** 的一个属性。<mark style="background: #FFF3A3A6;">上下文路径是应用程序的根URL的一部分，它定义了应用程序在服务器上的访问路径。
</mark>
---

### **1. 什么是上下文路径（Context Path）？**
<mark style="background: #FFF3A3A6;">上下文路径是应用程序的基础路径，所有请求的URL都会以这个路径开头。</mark>例如：
- 如果 `context-path` 设置为 `/myapp`，那么应用程序的访问路径就是 `http://localhost:8080/myapp`。
- 如果没有设置 `context-path`，默认路径是 `/`，应用程序的访问路径就是 `http://localhost:8080`。

---

### **2. 如何配置 `servlet.context-path`？**
在Spring Boot中，可以通过 `application.yml` 或 `application.properties` 文件来配置 `servlet.context-path`。

#### 示例（`application.yml`）：
```yaml
server:
  servlet:
    context-path: /myapp
```

#### 示例（`application.properties`）：
```properties
server.servlet.context-path=/myapp
```

---

### **3. `servlet.context-path` 的作用**
配置 `servlet.context-path` 后：
1. **URL路径**：所有请求的URL都会以 `/myapp` 开头。
   - 例如，`/hello` 的访问路径变为 `/myapp/hello`。
2. **静态资源路径**：静态资源（如HTML、CSS、JS文件）的路径也会以 `/myapp` 开头。
   - 例如，`/static/style.css` 的访问路径变为 `/myapp/static/style.css`。
3. **API路径**：所有Controller的路径也会以 `/myapp` 开头。
   - 例如，`@RequestMapping("/api")` 的访问路径变为 `/myapp/api`。

---

### **4. 示例项目**
以下是一个完整的示例项目，展示 `servlet.context-path` 的配置和使用。

#### 项目结构：
```
src
└── main
    ├── java
    │   └── com
    │       └── example
    │           └── MyApp.java
    ├── resources
    │   ├── static
    │   │   └── index.html
    │   └── application.yml
```

#### 代码：
```java
// MyApp.java
@SpringBootApplication
@RestController
public class MyApp {
    public static void main(String[] args) {
        SpringApplication.run(MyApp.class, args);
    }

    @GetMapping("/hello")
    public String hello() {
        return "Hello, World!";
    }
}
```

```yaml
# application.yml
server:
  servlet:
    context-path: /myapp
```

```html
<!-- src/main/resources/static/index.html -->
<!DOCTYPE html>
<html>
<head>
    <title>MyApp</title>
</head>
<body>
    <h1>Welcome to MyApp!</h1>
    <a href="/myapp/hello">Go to Hello Page</a>
</body>
</html>
```

#### 运行：
4. 启动应用程序。
5. 访问以下URL：
   - 首页：`http://localhost:8080/myapp/index.html`
   - API：`http://localhost:8080/myapp/hello`

---

### **5. 注意事项**
6. **路径冲突**：如果 `context-path` 设置为 `/`，应用程序的路径就是根路径，可能会与其他应用程序冲突。
7. **反向代理**：在使用反向代理（如Nginx）时，通常会在代理服务器中配置上下文路径，而不是在应用程序中配置。
8. **测试路径**：在编写测试代码时，需要注意路径的变化。

---

### **6. 总结**
`servlet.context-path` 是Spring Boot中用于配置应用程序上下文路径的属性。通过设置这个属性，可以定义应用程序的基础访问路径。例如：
- 配置为 `/myapp` 后，应用程序的访问路径变为 `http://localhost:8080/myapp`。
- 默认值为 `/`，即根路径。

这个属性在需要部署多个应用程序或与其他系统集成时非常有用。