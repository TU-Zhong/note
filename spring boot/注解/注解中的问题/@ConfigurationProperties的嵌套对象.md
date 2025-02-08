嵌套对象是 `@ConfigurationProperties` 的一个强大特性，它允许将配置文件中的**层级结构**映射到 Java 对象的**嵌套属性**中。下面我会详细解释嵌套对象的工作原理，并通过例子帮助你更好地理解。

---

### 嵌套对象的工作原理

1. **配置文件的结构**  
   在配置文件（如 `application.yml` 或 `application.properties`）中，属性可以按层级结构组织，例如：
   ```yaml
   app:
     database:
       url: jdbc:mysql://localhost:3306/mydb
       username: root
       password: password
   ```
   这里，`database` 是一个嵌套对象，包含了 `url`、`username` 和 `password` 三个属性。

2. **Java 类的结构**  
   在 Java 类中，可以通过嵌套类或对象来表示这种层级关系。例如：
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

       // 嵌套类
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

3. **Spring 的自动绑定**  
   Spring Boot 会自动将配置文件中的层级属性绑定到 Java 对象的嵌套属性中。具体步骤如下：
   - Spring 发现 `AppConfig` 类被标记为 `@ConfigurationProperties(prefix = "app")`，于是开始查找以 `app` 为前缀的配置项。
   - 当遇到 `database` 属性时，Spring 会检查 `AppConfig` 类中是否有 `database` 字段。
   - 如果 `database` 字段存在，并且是一个对象（如 `DatabaseConfig`），Spring 会递归地将 `database` 下的属性（如 `url`、`username`、`password`）绑定到 `DatabaseConfig` 对象中。

---

### 为什么可以直接使用 `url`、`username` 和 `password`？

在代码中，`url`、`username` 和 `password` 是 `DatabaseConfig` 类的属性，而 `DatabaseConfig` 是 `AppConfig` 的一个嵌套对象。Spring Boot 会**自动创建 `DatabaseConfig` 的实例**，并将其赋值给 `AppConfig` 的 `database` 字段。因此，你可以通过 `appConfig.getDatabase().getUrl()` 等方式访问这些属性。

---

### 示例代码

#### 1. **配置文件**
```yaml
app:
  database:
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: password
```

#### 2. **配置类**
```java
@Component
@ConfigurationProperties(prefix = "app")
public class AppConfig {

    private DatabaseConfig database;

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

#### 3. **使用配置类**
```java
@Service
public class AppService {

    private final AppConfig appConfig;

    @Autowired
    public AppService(AppConfig appConfig) {
        this.appConfig = appConfig;
    }

    public void printDatabaseConfig() {
        System.out.println("Database URL: " + appConfig.getDatabase().getUrl());
        System.out.println("Database Username: " + appConfig.getDatabase().getUsername());
        System.out.println("Database Password: " + appConfig.getDatabase().getPassword());
    }
}
```

---

### 总结

4. **嵌套对象的作用**  
   将配置文件中的层级结构映射到 Java 对象的嵌套属性中。

5. **为什么可以直接使用嵌套属性**  
   Spring Boot 会自动创建嵌套对象的实例，并将其绑定到父对象中，因此可以通过父对象访问嵌套对象的属性。

6. **使用场景**  
   当配置文件中的属性具有层级结构时，使用嵌套对象可以更清晰地组织代码和配置。

希望这个解释能帮助你理解嵌套对象的用法！如果还有疑问，欢迎继续提问！