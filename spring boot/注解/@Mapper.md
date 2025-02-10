在 Spring Boot 中，`@Mapper` 是 **MyBatis** 框架中的一个注解，用于标记一个接口作为 **MyBatis 的 Mapper 接口**。它的主要作用是告诉 MyBatis，这个接口是一个数据访问层（DAO）的映射器，MyBatis 会自动为其生成实现类，从而简化数据库操作。
[[服务层，mapper和实体类]]
[[@Mapper中的参数及其含义]]

---

### `@Mapper` 的作用

1. **标识 Mapper 接口**  
   `@Mapper` 注解用于标记一个接口是 MyBatis 的 Mapper 接口。MyBatis 会扫描这些接口，并为其生成代理实现类。

2. **简化数据库操作**  
   通过在 Mapper 接口中定义方法，并配合 MyBatis 的注解或 XML 配置文件，可以直接操作数据库，而不需要编写实现类。

3. **与 Spring Boot 集成**  
   在 Spring Boot 中，`@Mapper` 注解的接口会被自动注册为 Spring Bean，可以直接在其他组件中注入使用。

---

### `@Mapper` 的使用

#### 1. **添加依赖**
首先，需要在 `pom.xml` 中添加 MyBatis 和 MyBatis-Spring Boot Starter 依赖：
```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.3.0</version>
</dependency>
```

---

#### 2. **定义 Mapper 接口**
使用 `@Mapper` 注解标记接口，并定义数据库操作方法。

```java
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

import java.util.List;

@Mapper // 标记为 MyBatis Mapper 接口
public interface UserMapper {

    @Select("SELECT * FROM users")
    List<User> findAllUsers();

    @Select("SELECT * FROM users WHERE id = #{id}")
    User findUserById(int id);
}
```

---

#### 3. **实体类**
定义与数据库表对应的实体类。

```java
public class User {
    private int id;
    private String name;
    private String email;

    // Getter 和 Setter 方法
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

---

#### 4. **使用 Mapper 接口**
在其他组件（如 Service）中注入 Mapper 接口并使用。

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {

    private final UserMapper userMapper;

    @Autowired
    public UserService(UserMapper userMapper) {
        this.userMapper = userMapper;
    }

    public List<User> getAllUsers() {
        return userMapper.findAllUsers();
    }

    public User getUserById(int id) {
        return userMapper.findUserById(id);
    }
}
```

---

### `@Mapper` 的高级用法

#### 1. **与 XML 配置文件结合**
可以将 SQL 语句写在 XML 配置文件中，而不是直接写在注解中。

**Mapper 接口**：
```java
@Mapper
public interface UserMapper {
    List<User> findAllUsers();
    User findUserById(int id);
}
```

**XML 配置文件（`UserMapper.xml`）**：
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.example.mapper.UserMapper">
    <select id="findAllUsers" resultType="com.example.entity.User">
        SELECT * FROM users
    </select>

    <select id="findUserById" resultType="com.example.entity.User">
        SELECT * FROM users WHERE id = #{id}
    </select>
</mapper>
```

---

#### 2. **批量扫描 Mapper 接口**
如果项目中有多个 Mapper 接口，可以使用 `@MapperScan` 注解批量扫描，而不是在每个接口上单独添加 `@Mapper`。

```java
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.example.mapper") // 扫描指定包下的所有 Mapper 接口
public class MyApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```

---

### 总结

4. **`@Mapper` 的作用**  
   - 标识 MyBatis 的 Mapper 接口。
   - 自动生成实现类，简化数据库操作。
   - 与 Spring Boot 集成，自动注册为 Bean。

5. **使用场景**  
   - 在 Spring Boot 项目中，使用 MyBatis 作为 ORM 框架时。
   - 需要简化数据库操作的代码。

6. **高级用法**  
   - 结合 XML 配置文件使用。
   - 使用 `@MapperScan` 批量扫描 Mapper 接口。

通过 `@Mapper`，可以大大简化 MyBatis 在 Spring Boot 中的使用，提升开发效率。如果还有疑问，欢迎继续提问！