在 Spring Boot 中，`@ConfigurationProperties` 是一个强大的注解，用于将配置文件中的属性批量绑定到一个 Java Bean 中。它通常用于将一组相关的配置属性映射到一个对象中，而不是通过 `@Value` 逐个注入。

---

### `@ConfigurationProperties` 的使用步骤

#### 1. **添加依赖**
Spring Boot 2.2 及以上版本需要显式添加 `spring-boot-configuration-processor` 依赖来支持配置属性的元数据生成（IDE 提示功能）：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

---

#### 2. **定义配置类**
使用 `@ConfigurationProperties` 注解标记一个类，并通过 `prefix` 指定配置项的前缀。

```java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "app") // 配置项的前缀为 app
public class AppConfig {

    private String name;
    private String version;
    private String description;
    private List<String> servers;
    private Map<String, String> metadata;

    // Getter 和 Setter 方法
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getVersion() {
        return version;
    }

    public void setVersion(String version) {
        this.version = version;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public List<String> getServers() {
        return servers;
    }

    public void setServers(List<String> servers) {
        this.servers = servers;
    }

    public Map<String, String> getMetadata() {
        return metadata;
    }

    public void setMetadata(Map<String, String> metadata) {
        this.metadata = metadata;
    }
}
```

---

#### 3. **配置文件**
在 `application.properties` 或 `application.yml` 中定义配置项。

**`application.properties` 示例**：
```properties
app.name=MyApplication
app.version=1.0.0
app.description=This is a Spring Boot application
app.servers[0]=server1
app.servers[1]=server2
app.metadata.key1=value1
app.metadata.key2=value2
```

**`application.yml` 示例**：
```yaml
app:
  name: MyApplication
  version: 1.0.0
  description: This is a Spring Boot application
  servers:
    - server1
    - server2
  metadata:
    key1: value1
    key2: value2
```

---

#### 4. **启用配置属性绑定**
在 Spring Boot 主类或配置类上添加 `@EnableConfigurationProperties` 注解，启用配置属性绑定。

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.properties.EnableConfigurationProperties;

@SpringBootApplication
@EnableConfigurationProperties(AppConfig.class) // 启用配置属性绑定
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

---

#### 5. **使用配置类**
在其他组件中注入配置类并使用。

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class AppService {

    private final AppConfig appConfig;

    @Autowired
    public AppService(AppConfig appConfig) {
        this.appConfig = appConfig;
    }

    public void printConfig() {
        System.out.println("App Name: " + appConfig.getName());
        System.out.println("App Version: " + appConfig.getVersion());
        System.out.println("App Description: " + appConfig.getDescription());
        System.out.println("Servers: " + appConfig.getServers());
        System.out.println("Metadata: " + appConfig.getMetadata());
    }
}
```

---

### `@ConfigurationProperties` 的高级用法

#### 1. **嵌套对象**
支持将配置项绑定到嵌套对象中。

Q:  **我不太明白那个嵌套对象是怎么用的，为什么没有注入database就可以直接使用url,username和password?**
[[@ConfigurationProperties的嵌套对象]]

**配置文件**：
```yaml
app:
  database:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: password
```

**配置类**：
```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {

    private DatabaseConfig database;

    // Getter 和 Setter 方法
    public DatabaseConfig getDatabase() {
        return database;
    }

    public void setDatabase(DatabaseConfig database) {
        this.database = database;
    }

    public static class DatabaseConfig {
        private String url;
        private String username;
        private String password;

        // Getter 和 Setter 方法
        public String getUrl() {
            return url;
        }

        public void setUrl(String url) {
            this.url = url;
        }

        public String getUsername() {
            return username;
        }

        public void setUsername(String username) {
            this.username = username;
        }

        public String getPassword() {
            return password;
        }

        public void setPassword(String password) {
            this.password = password;
        }
    }
}
```

---

#### 2. **校验配置属性**
可以使用 `@Validated` 和 JSR-303 校验注解对配置属性进行校验。

**依赖**：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

**配置类**：
```java
import org.springframework.validation.annotation.Validated;
import javax.validation.constraints.NotBlank;

@Component
@ConfigurationProperties(prefix = "app")
@Validated
public class AppConfig {

    @NotBlank
    private String name;

    // Getter 和 Setter 方法
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

---

### 总结
`@ConfigurationProperties` 的用法包括：
1. 定义配置类并指定前缀。
2. 在配置文件中定义属性。
3. 启用配置属性绑定。
4. 注入配置类并使用。

它适用于批量绑定配置属性，并支持嵌套对象、集合、Map 等复杂结构，是 Spring Boot 中管理配置的推荐方式。