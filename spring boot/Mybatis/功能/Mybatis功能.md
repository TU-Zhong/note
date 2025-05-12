MyBatis 作为一个灵活的持久层框架，除了你已经了解的 `<discriminator>` 和嵌套查询外，还有许多非常常见且实用的用法。你提到的 `LIMIT` 就是分页查询中一个典型的例子。

以下是一些 MyBatis 中其他常见的用法：

### 1. 分页查询 (LIMIT / OFFSET)

在Web应用中，分页显示数据是非常常见的需求。MyBatis 本身不直接提供跨数据库的统一分页标签，因为不同数据库的分页SQL语法不同。你需要自己编写带分页逻辑的SQL，或者使用分页插件。

- **手动编写分页SQL (以MySQL为例):** 在SQL语句中使用特定数据库的 `LIMIT` 和 `OFFSET` 子句。
    
    XML
    
    ```
    <select id="findUsersPaged" resultType="com.example.model.User">
        SELECT id, name, email
        FROM User
        ORDER BY id
        LIMIT #{offset}, #{rowCount}
    </select>
    ```
    
    Mapper接口方法可能像这样：
    
    Java
    
    ```
    List<User> findUsersPaged(@Param("offset") int offset, @Param("rowCount") int rowCount);
    // 或者使用 Map
    List<User> findUsersPaged(Map<String, Object> params); // params.put("offset", 0); params.put("rowCount", 10);
    ```
    
- **使用 `RowBounds` 对象 (不推荐用于大数据量分页):** MyBatis 提供了一个 `RowBounds` 类，可以在调用 mapper 方法时传入。
    
    Java
    
    ```
    // 在 Service 层或调用处
    int offset = 0;
    int limit = 10;
    RowBounds rowBounds = new RowBounds(offset, limit);
    List<User> users = userMapper.findAllUsers(rowBounds); // Mapper方法不需要显式接收RowBounds参数
    ```
    
    XML
    
    ```
    <select id="findAllUsers" resultType="com.example.model.User">
        SELECT id, name, email FROM User ORDER BY id
    </select>
    ```
    
    **注意**：如果JDBC驱动不支持或未正确配置，`RowBounds` 可能会导致 MyBatis 将所有结果从数据库加载到内存中，然后再进行分页，这对于大数据集是非常低效的。**因此，显式在SQL中写LIMIT子句或者使用分页插件通常是更好的选择。**
    
- **使用分页插件 (如 PageHelper):** 这是非常流行的方式。插件会自动拦截你的SQL，并根据你的配置和数据库类型动态添加分页语句。 例如，使用 PageHelper，你的代码可能很简单：
    
    Java
    
    ```java
    // 在 Service 层
    PageHelper.startPage(1, 10); // 查询第一页，每页10条
    List<User> users = userMapper.findAllUsers(); // 普通的查询方法
    PageInfo<User> pageInfo = new PageInfo<>(users); // pageInfo 包含分页信息和结果列表
    // total = pageInfo.getTotal();
    // pages = pageInfo.getPages();
    ```
    
    Mapper XML 不需要任何特殊的分页SQL。
    

### 2. 动态SQL

这是 MyBatis 最强大的特性之一，允许你根据条件动态构建SQL语句。

- **`<if test="...">`**: 条件判断。
    
    XML
    
    ```
    <select id="findActiveUsersByName" resultType="com.example.model.User">
        SELECT * FROM User
        WHERE status = 'ACTIVE'
        <if test="name != null and name != ''">
            AND name LIKE CONCAT('%', #{name}, '%')
        </if>
    </select>
    ```
    
- **`<choose>`, `<when>`, `<otherwise>`**: 类似于Java的 `switch` 语句。
    
    XML
    
    ```
    <select id="findUsersByCriteria" resultType="com.example.model.User">
        SELECT * FROM User
        <where>
            <choose>
                <when test="name != null">
                    name = #{name}
                </when>
                <when test="email != null">
                    AND email = #{email}
                </when>
                <otherwise>
                    AND status = 'GUEST'
                </otherwise>
            </choose>
        </where>
    </select>
    ```
    
- **`<where>`**: 智能地处理 `AND` 或 `OR` 前缀。如果内部条件都不成立，则不生成 `WHERE` 子句。如果内部条件成立，会自动添加 `WHERE`，并移除多余的 `AND`/`OR`。
    
- **`<set>` (用于 `UPDATE`语句)**: 智能地处理逗号。会自动添加 `SET`，并移除多余的逗号。
    
    XML
    
    ```
    <update id="updateUserSelective">
        UPDATE User
        <set>
            <if test="name != null">name = #{name},</if>
            <if test="email != null">email = #{email},</if>
            <if test="status != null">status = #{status},</if>
        </set>
        WHERE id = #{id}
    </update>
    ```
    
- **`<foreach>`**: 用于迭代集合，常用于构建 `IN` 子句或批量插入/更新。
    
    XML
    
    ```
    <select id="findUsersByIds" resultType="com.example.model.User">
        SELECT * FROM User
        WHERE id IN
        <foreach item="userId" collection="list" open="(" separator="," close=")">
            #{userId}
        </foreach>
    </select>
    ```
    
- **`<trim>`**: 更通用的前缀/后缀处理。
    
- **`<bind name="variableName" value="OGNLExpression"/>`**: 创建一个可以在后续动态SQL中使用的变量。
    
    XML
    
    ```
    <select id="findUserWithPrefixedName" resultType="com.example.model.User">
        <bind name="pattern" value="'%' + nameParam + '%'"/>
        SELECT * FROM User WHERE name LIKE #{pattern}
    </select>
    ```
    

### 3. 返回自动生成的主键 (Generated Keys)

在执行 `INSERT` 语句后，经常需要获取数据库自动生成的主键。

- **`useGeneratedKeys` 和 `keyProperty`**:
    
    XML
    
    ```
    <insert id="insertUser" parameterType="com.example.model.User"
            useGeneratedKeys="true" keyProperty="id">
        INSERT INTO User (name, email, status)
        VALUES (#{name}, #{email}, #{status})
    </insert>
    ```
    
    执行后，传入的 `User` 对象的 `id` 属性会被自动填充上生成的主键值。
    
- **`<selectKey>`**: 对于不支持 `useGeneratedKeys` 的数据库（如Oracle早期版本使用序列），或需要更复杂主键生成逻辑的情况。
    
    XML
    
    ```
    <insert id="insertUserWithSelectKey" parameterType="com.example.model.User">
        <selectKey keyProperty="id" resultType="int" order="BEFORE">
            SELECT NEXTVAL('user_id_seq') </selectKey>
        INSERT INTO User (id, name, email)
        VALUES (#{id}, #{name}, #{email})
    </insert>
    ```
    
    `order="BEFORE"` 表示在 `INSERT` 之前执行 `<selectKey>`，`order="AFTER"` 则表示之后执行。
    

### 4. 批量操作 (Batch Operations)

- **使用 `<foreach>` 进行批量插入/更新 (部分数据库支持多VALUES插入或拼接更新):**
    
    XML
    
    ```
    <insert id="batchInsertUsers" parameterType="java.util.List">
        INSERT INTO User (name, email) VALUES
        <foreach collection="list" item="user" separator=",">
            (#{user.name}, #{user.email})
        </foreach>
    </insert>
    ```
    
- **使用 `ExecutorType.BATCH` (推荐的真正批处理方式):** 在Java代码中获取 `SqlSession` 时指定执行器类型。
    
    Java
    
    ```
    try (SqlSession sqlSession = sqlSessionFactory.openSession(ExecutorType.BATCH)) {
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        for (User user : userList) {
            mapper.insertUser(user); // 语句会被缓存起来
        }
        sqlSession.commit(); // 或者 sqlSession.flushStatements() 后手动处理事务
    }
    ```
    
    这种方式会利用JDBC驱动的批处理能力，性能通常更好。
    

### 5. SQL 片段 (`<sql>` 和 `<include>`)

定义可重用的SQL代码片段，避免重复。

XML

```
<sql id="baseUserColumns">
    id, name, email, status
</sql>

<select id="findUserById" resultType="com.example.model.User">
    SELECT <include refid="baseUserColumns"/>
    FROM User
    WHERE id = #{id}
</select>

<select id="findAllActiveUsers" resultType="com.example.model.User">
    SELECT <include refid="baseUserColumns"/>
    FROM User
    WHERE status = 'ACTIVE'
</select>
```

### 6. 缓存 (Caching)

MyBatis 提供一级缓存（SqlSession级别，默认开启）和二级缓存（Mapper级别/namespace级别，需手动配置）。

- **启用二级缓存：** 在Mapper XML文件中添加 `<cache/>` 标签。
    
    XML
    
    ```
    <mapper namespace="com.example.mapper.UserMapper">
        <cache eviction="LRU" flushInterval="60000" size="512" readOnly="true"/>
        </mapper>
    ```
    
    被缓存的对象需要实现 `java.io.Serializable` 接口。

### 7. 自定义类型处理器 (Type Handlers)

用于Java类型与JDBC类型之间的转换，当MyBatis默认的处理器不满足需求时使用。例如，处理枚举类型、Joda-Time或Java 8时间API与数据库特定类型的映射。

### 8. 插件 (Plugins / Interceptors)

允许在MyBatis执行过程中的特定点（如SQL预处理、参数设置、结果集处理、执行器方法调用）进行拦截和修改。分页插件、性能监控插件、自定义加解密插件等都是通过这个机制实现的。

### 9. 调用存储过程

MyBatis 支持调用数据库存储过程。

XML

```
<select id="callUserProcedure" statementType="CALLABLE"
        parameterType="java.util.Map" resultType="com.example.model.User">
    {CALL get_user_details(
        #{userId, mode=IN, jdbcType=INTEGER},
        #{userName, mode=OUT, jdbcType=VARCHAR},
        #{userEmail, mode=OUT, jdbcType=VARCHAR}
    )}
</select>
```

参数需要指定 `mode` (IN, OUT, INOUT) 和 `jdbcType`。OUT参数的值会在调用后设置回传入的参数对象（通常是Map或JavaBean）。

这些只是MyBatis众多功能中的一部分，但它们构成了日常开发中最常用和最有价值的特性。掌握它们能让你更高效地使用MyBatis。