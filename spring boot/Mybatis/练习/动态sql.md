好的，这个动态条件查询客户的练习非常好，它能让你熟练运用 MyBatis 的动态 SQL 功能，特别是 `<if>` 和 `<where>` 或 `<trim>` 标签。

**目标：** 编写一个方法 `findCustomersByCriteria(Customer criteria)`，根据传入的 `Customer` 对象中的非空属性来动态构建查询条件。

- `customerName` 和 `contactEmail` 支持模糊匹配 (LIKE)。
- `city` 支持精确匹配。
- 如果 `criteria` 对象中的某个属性为 `null` 或空字符串，则该条件不加入查询。

---

### 1. Mapper 接口方法 (`CustomerMapper.java`)

Java

```java
package com.example.mapper; // 你的包名

import com.example.model.Customer;
import java.util.List;

public interface CustomerMapper {
    /**
     * 根据动态条件查询客户列表。
     * @param criteria 包含查询条件（customerName, contactEmail, city）的 Customer 对象。
     * null 或空字符串的属性将被忽略。
     * @return 符合条件的客户列表。
     */
    List<Customer> findCustomersByCriteria(Customer criteria);
}
```

---

### 2. Mapper XML 配置 (`CustomerMapper.xml`)

我们将展示两种实现动态 `WHERE` 子句的方式：使用 `<where>` 标签（更常见和简洁）和使用 `<trim>` 标签（更灵活，可以实现 `<where>` 的功能）。

#### 方案一：使用 `<where>` 标签 (推荐)

`<where>` 标签非常智能：

- 只有当其内部至少有一个条件被渲染时，它才会插入 `WHERE` 关键字。
- 它会自动去除条件前面多余的 `AND` 或 `OR`。

XML

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.CustomerMapper">

    <select id="findCustomersByCriteria" parameterType="com.example.model.Customer" resultType="com.example.model.Customer">
        SELECT customerId, customerName, contactEmail, city
        FROM Customers
        <where>
            <if test="customerName != null and customerName != ''">
                AND customerName LIKE CONCAT('%', #{customerName}, '%')
            </if>
            <if test="contactEmail != null and contactEmail != ''">
                AND contactEmail LIKE CONCAT('%', #{contactEmail}, '%')
            </if>
            <if test="city != null and city != ''">
                AND city = #{city}
            </if>
        </where>
        ORDER BY customerId ASC
    </select>

</mapper>
```

**解释:**

- **`parameterType="com.example.model.Customer"`**: 传入的 `criteria` 对象类型。
- **`resultType="com.example.model.Customer"`**: 查询结果列表中每个元素的类型。
- **`<where>`**: 包裹所有动态条件。
- **`<if test="customerName != null and customerName != ''">`**:
    - `test` 属性中是 OGNL 表达式。这里检查 `customerName` 是否不为 `null` 且不为空字符串。
    - 如果条件成立，内部的 SQL 片段 `AND customerName LIKE ...` 才会被包含到最终的SQL中。
    - `#{customerName}` 会被替换为 `criteria.getCustomerName()` 的值。
- **`CONCAT('%', #{customerName}, '%')`**: 这是 MySQL 中用于模糊查询的字符串拼接方式。
    - 如果你想让 MyBatis 处理 `%` 通配符的拼接，可以使用 `<bind>` 标签，或者在 Java 代码中准备好带 `%` 的参数值。

#### 方案二：使用 `<trim>` 标签

`<trim>` 标签提供了更底层的控制，可以用来实现与 `<where>` 类似甚至更复杂的前后缀添加/移除逻辑。

XML

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.CustomerMapper">

    <select id="findCustomersByCriteriaWithTrim" parameterType="com.example.model.Customer" resultType="com.example.model.Customer">
        SELECT customerId, customerName, contactEmail, city
        FROM Customers
        <trim prefix="WHERE" prefixOverrides="AND |OR ">
            <if test="customerName != null and customerName != ''">
                AND customerName LIKE CONCAT('%', #{customerName}, '%')
            </if>
            <if test="contactEmail != null and contactEmail != ''">
                AND contactEmail LIKE CONCAT('%', #{contactEmail}, '%')
            </if>
            <if test="city != null and city != ''">
                AND city = #{city}
            </if>
        </trim>
        ORDER BY customerId ASC
    </select>

</mapper>
```

**`<trim>` 标签用在哪里？** 如上所示，`<trim>` 标签可以用在需要动态添加 `WHERE` 子句并处理其内部条件前缀（如 `AND` 或 `OR`）的地方。

- `prefix="WHERE"`: 当 `<trim>` 内部有任何 `<if>` 条件满足时，它会在整个条件块前加上 `WHERE` 关键字。
- `prefixOverrides="AND |OR "` : 这个属性非常关键。它会检查 `<trim>` 内部实际生成的 SQL 的开头。如果开头是 `AND` (注意有个空格) 或者 `OR`，这些前缀就会被移除。这确保了即使第一个满足的 `<if>` 条件带有 `AND`，最终生成的 SQL 也是合法的。

`<trim>` 标签也常用于其他场景，比如 `<set>` 标签的功能（`suffixOverrides=","` 移除UPDATE语句中末尾多余的逗号），或者在 `<foreach>` 中自定义分隔符和前后缀。

#### 处理 LIKE 模糊查询的另一种方式 (使用 `<bind>`)

为了让XML更简洁，或者为了跨数据库兼容性（不同数据库字符串拼接函数不同），可以使用 `<bind>` 标签预先处理 `LIKE` 的模式字符串。

XML

```xml
<select id="findCustomersByCriteriaWithBind" parameterType="com.example.model.Customer" resultType="com.example.model.Customer">
    SELECT customerId, customerName, contactEmail, city
    FROM Customers
    <where>
        <if test="customerName != null and customerName != ''">
            <bind name="customerNamePattern" value="'%' + customerName + '%'" />
            AND customerName LIKE #{customerNamePattern}
        </if>
        <if test="contactEmail != null and contactEmail != ''">
            <bind name="emailPattern" value="'%' + contactEmail + '%'" />
            AND contactEmail LIKE #{emailPattern}
        </if>
        <if test="city != null and city != ''">
            AND city = #{city}
        </if>
    </where>
    ORDER BY customerId ASC
</select>
```

- **`<bind name="customerNamePattern" value="'%' + customerName + '%'" />`**:
    - `name="customerNamePattern"`: 定义了一个名为 `customerNamePattern` 的变量。
    - `value="'%' + customerName + '%'"`: 使用 OGNL 表达式来构建值。这里，它将 `criteria.getCustomerName()` 的值前后加上了 `%` 通配符。注意 `customerName` 是 `criteria` 对象的属性名。
    - 之后就可以在 SQL 中使用 `#{customerNamePattern}`。

---

### 3. 如何调用和测试

Java

```java
// 在你的服务层或测试类中
public void testFindCustomers() {
    try (SqlSession session = MyBatisSqlSessionFactoryUtil.openSession()) {
        CustomerMapper customerMapper = session.getMapper(CustomerMapper.class);

        // 场景1: 没有查询条件 (期望返回所有客户)
        Customer criteria1 = new Customer();
        List<Customer> allCustomers = customerMapper.findCustomersByCriteria(criteria1);
        System.out.println("所有客户: " + allCustomers.size());
        allCustomers.forEach(System.out::println);

        System.out.println("\n---------------------------------\n");

        // 场景2: 按名称模糊查询
        Customer criteria2 = new Customer();
        criteria2.setCustomerName("张"); // 假设数据库中有姓张的客户
        List<Customer> customersByName = customerMapper.findCustomersByCriteria(criteria2);
        System.out.println("名称包含 '张' 的客户: " + customersByName.size());
        customersByName.forEach(System.out::println);

        System.out.println("\n---------------------------------\n");

        // 场景3: 按城市精确查询
        Customer criteria3 = new Customer();
        criteria3.setCity("台北市");
        List<Customer> customersByCity = customerMapper.findCustomersByCriteria(criteria3);
        System.out.println("城市为 '台北市' 的客户: " + customersByCity.size());
        customersByCity.forEach(System.out::println);

        System.out.println("\n---------------------------------\n");

        // 场景4: 组合条件查询 (名称包含 '李' 且城市为 '高雄市')
        Customer criteria4 = new Customer();
        criteria4.setCustomerName("李");
        criteria4.setCity("高雄市");
        List<Customer> customersByCombined = customerMapper.findCustomersByCriteria(criteria4);
        System.out.println("名称包含 '李' 且城市为 '高雄市' 的客户: " + customersByCombined.size());
        customersByCombined.forEach(System.out::println);

    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

这个练习题解展示了如何使用 MyBatis 的动态 SQL 功能（特别是 `<if>` 结合 `<where>` 或 `<trim>`）来构建灵活的查询。`<where>` 通常是处理动态 `WHERE` 子句的首选，因为它更简洁；而 `<trim>` 提供了更细致的控制，可以用于实现 `<where>` 的功能以及其他需要自定义前后缀处理的场景。