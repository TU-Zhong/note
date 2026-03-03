# 淘淘外卖 - 后端系统

  

基于Spring Boot 3.5.5的B2C在线外卖平台后端系统，完整实现了四大核心技术特性。

  

## 🚀 技术栈

  

- **框架**: Spring Boot 3.5.5

- **数据库**: SQL Server

- **ORM**: MyBatis 3.0.4

- **缓存**: Redis (Spring Data Redis)

- **认证**: JWT (jjwt 0.12.6)

- **构建工具**: Maven

- **Java版本**: 17

  

## 🎯 核心技术实现

  

### 1. 基于MyBatis的动态SQL与复杂查询

- ✅ **动态条件查询**: 使用`<if>`标签实现灵活的条件组合

- ✅ **分页查询**: 集成PageHelper实现高效分页

- ✅ **复杂关联查询**: 支持多表关联和嵌套查询

- ✅ **SQL优化**: 原生SQL控制，性能优化

  

### 2. Redis缓存实现

- ✅ **商家营业状态缓存**: 高频访问数据缓存优化

- ✅ **购物车Hash结构**: 用户购物车数据Redis存储

- ✅ **Cache-Aside模式**: 标准缓存更新策略

- ✅ **连接池配置**: Lettuce连接池优化

  

### 3. JWT无状态认证

- ✅ **双端支持**: C端用户和B端管理员分离认证

- ✅ **自定义Claims**: userId、userType、username等信息

- ✅ **拦截器统一认证**: 基于路径的权限控制

- ✅ **安全密钥**: 256位安全密钥配置

  

### 4. AOP切面编程

- ✅ **自动填充公共字段**: create_time、update_time、create_user、update_user

- ✅ **注解驱动**: @AutoFill注解标记需要填充的方法

- ✅ **反射机制**: 动态调用setter方法

- ✅ **ThreadLocal上下文**: 用户信息传递

  

## 项目结构

  

```

src/main/java/com/taotao/

├── aspect/              # AOP切面

├── common/              # 公共组件

│   ├── annotation/      # 自定义注解

│   ├── constant/        # 常量类

│   ├── context/         # 上下文工具

│   ├── exception/       # 异常类

│   ├── handler/         # 异常处理器

│   ├── properties/      # 配置属性

│   └── utils/           # 工具类

├── config/              # 配置类

├── controller/          # 控制器

│   ├── admin/           # 管理端接口

│   └── user/            # 用户端接口

├── interceptor/         # 拦截器

├── mapper/              # 数据访问层

├── pojo/                # 实体类

│   └── dto/             # 数据传输对象

└── service/             # 服务层

    └── impl/            # 服务实现

```

  

## 环境要求

  

- JDK 17+

- Maven 3.6+

- SQL Server 2019+

- Redis 6.0+

  

## 快速开始

  

### 1. 数据库配置

  

1. 创建SQL Server数据库 `taotao_takeout`

2. 执行 `src/main/resources/sql/schema.sql` 创建表结构

3. 执行 `src/main/resources/sql/data.sql` 插入测试数据

  

### 2. 配置文件

  

修改 `src/main/resources/application.yml` 中的数据库和Redis连接信息：

  

```yaml

spring:

  datasource:

    url: jdbc:sqlserver://localhost:1433;databaseName=taotao_takeout

    username: your_username

    password: your_password

  data:

    redis:

      host: localhost

      port: 6379

```

  

### 3. 启动应用

  

```bash

mvn spring-boot:run

```

  

应用将在 `http://localhost:8080/api` 启动

  

## API接口

  

### 管理端接口 (需要管理员权限)

- `POST /api/admin/user/login` - 管理员登录

- `GET /api/admin/shop/status` - 获取店铺状态

- `PUT /api/admin/shop/{status}` - 设置店铺状态

- `GET /api/admin/dish/page` - 菜品分页查询

- `POST /api/admin/dish` - 新增菜品

  

### 用户端接口 (需要用户权限)

- `POST /api/user/user/login` - 用户登录

- `GET /api/user/shop/status` - 获取店铺状态

- `POST /api/user/shoppingCart/add` - 添加购物车

- `GET /api/user/shoppingCart/list` - 查看购物车

  

## 测试

  

运行单元测试：

  

```bash

mvn test

```

  

测试覆盖的功能：

- 用户登录认证

- Redis缓存操作

- 购物车功能

- JWT令牌生成和解析

- 动态SQL查询

  

## 核心功能演示

  

### 1. 动态SQL查询示例

  

```xml

<select id="pageQuery" resultType="com.taotao.pojo.Dish">

    select * from dishes

    <where>

        <if test="name != null and name != ''">

            and name like concat('%',#{name},'%')

        </if>

        <if test="categoryId != null">

            and category_id = #{categoryId}

        </if>

        <if test="status != null">

            and status = #{status}

        </if>

    </where>

    order by create_time desc

</select>

```

  

### 2. Redis购物车操作

  

```java

// 添加商品到购物车

String key = "cart:" + userId;

String itemKey = "dish:" + dishId;

redisTemplate.opsForHash().put(key, itemKey, quantity);

```

  

### 3. JWT认证流程

  

```java

// 生成JWT

Map<String, Object> claims = new HashMap<>();

claims.put("userId", user.getId());

claims.put("userType", user.getUserType());

String token = JwtUtil.createJWT(secret, expireTime, claims);

```

  

## 注意事项

  

1. 首次运行前请确保SQL Server和Redis服务已启动

2. 测试数据中的密码已使用MD5加密

3. JWT密钥请在生产环境中修改为更安全的值

4. Redis缓存过期时间可根据业务需求调整

  

## 贡献

  

欢迎提交Issue和Pull Request来改进项目。

  

## 许可证

  

MIT License