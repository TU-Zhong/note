在 MyBatis 中处理“一对多”关系（例如一个顾客有多张订单）时，**嵌套查询 (Nested Query)** 和 **嵌套结果 (Nested Result)** 是两种最核心的方法。

这两种方法的目标都是一样的：查询一个顾客 (Customer) 对象，并将其关联的订单 (Order) 列表作为 `List<Order>` 填充到 Customer 对象的 `orders` 属性中。

下面我们就用您外卖项目的“顾客”和“订单”场景来分别实现这两个 Demo。

### 场景准备：数据表和实体类

假设我们有两张表：顾客表 (`customer`) 和订单表 (`orders`)。

**1. SQL 数据表**

SQL

```java
-- 顾客表
CREATE TABLE customer (
    customer_id INT PRIMARY KEY IDENTITY(1,1),
    customer_name NVARCHAR(100) NOT NULL,
    phone VARCHAR(20)
);

-- 订单表 (一个顾客可以有多个订单)
CREATE TABLE orders (
    order_id INT PRIMARY KEY IDENTITY(1,1),
    customer_id INT NOT NULL, -- 外键, 关联到 customer 表
    order_time DATETIME DEFAULT GETDATE(),
    total_amount DECIMAL(10, 2),
    CONSTRAINT FK_customer_id FOREIGN KEY (customer_id) REFERENCES customer(customer_id)
);

-- 插入模拟数据
INSERT INTO customer (customer_name, phone) VALUES (N'张三', '13800138000');
INSERT INTO customer (customer_name, phone) VALUES (N'李四', '13900139000');

INSERT INTO orders (customer_id, total_amount) VALUES (1, 58.50);
INSERT INTO orders (customer_id, total_amount) VALUES (1, 32.00);
INSERT INTO orders (customer_id, total_amount) VALUES (2, 79.00);
```

**2. Java 实体类 (POJO)**

`Order.java`

Java

```java
import java.math.BigDecimal;
import java.util.Date;
// (省略 getter, setter, toString)
public class Order {
    private Integer orderId;
    private Integer customerId;
    private Date orderTime;
    private BigDecimal totalAmount;
}
```

`Customer.java` (核心：包含一个订单列表)

Java

```java
import java.util.List;
// (省略 getter, setter, toString)
public class Customer {
    private Integer customerId;
    private String customerName;
    private String phone;
    
    // 核心：一对多的关系
    private List<Order> orders;
}
```

---

### Demo 1：嵌套查询 (Nested Query)

**原理**：这是“N+1”查询。

1. 先执行一个 SQL 查询顾客信息（“1”查询）。
    
2. 根据查询到的 `customer_id`，**再单独**去执行另一个 SQL 查询该顾客的所有订单（“N”查询）。
    

这种方法配置简单，但性能较差。

**1. Mapper 接口 (`CustomerMapper.java`)**

Java

```java
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface CustomerMapper {
    /**
     * Demo 1: 使用嵌套查询
     */
    Customer findCustomerById_NestedQuery(Integer id);
    
    /**
     * Demo 2: 使用嵌套结果
     */
    Customer findCustomerById_NestedResult(Integer id);
}
```

_（注意：为了这个 Demo，我们还需要一个 `OrderMapper` 来提供那个“N”查询）_

**`OrderMapper.java`**

Java

```java
import org.apache.ibatis.annotations.Mapper;
import java.util.List;

@Mapper
public interface OrderMapper {
    /**
     * 辅助查询：根据顾客ID查询订单列表 (供 Demo 1 使用)
     */
    List<Order> findOrdersByCustomerId(Integer customerId);
}
```

**2. Mapper XML (`CustomerMapper.xml`)**

XML

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yourproject.mapper.CustomerMapper">

    <resultMap id="CustomerWithOrders_NestedQuery" type="com.yourproject.model.Customer">
        <id property="customerId" column="customer_id" />
        <result property="customerName" column="customer_name" />
        <result property="phone" column="phone" />
        
        <collection property="orders" 
                    select="com.yourproject.mapper.OrderMapper.findOrdersByCustomerId" 
                    column="customer_id"
                    javaType="java.util.List"
                    ofType="com.yourproject.model.Order" />
    </resultMap>

    <select id="findCustomerById_NestedQuery" resultMap="CustomerWithOrders_NestedQuery">
        SELECT * FROM customer WHERE customer_id = #{id}
    </select>
    
    </mapper>
```

**3. `OrderMapper.xml`**

XML

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yourproject.mapper.OrderMapper">

    <select id="findOrdersByCustomerId" resultType="com.yourproject.model.Order">
        SELECT 
            order_id as orderId, 
            customer_id as customerId, 
            order_time as orderTime, 
            total_amount as totalAmount
        FROM orders 
        WHERE customer_id = #{id}
    </select>
    
</mapper>
```

**总结 (Demo 1)**

- **优点**：实现简单，SQL 语句清晰分离。
    
- **缺点**：**N+1 问题**。如果你查询 10 个顾客，会执行 1 (查顾客) + 10 (查订单) = 11 次 SQL。性能开销大。
    

---

### Demo 2：嵌套结果 (Nested Result)

**原理**：这是“JOIN”查询。

1. 执行**一个**包含 `JOIN` 的 SQL 查询，一次性获取所有顾客和他们对应的订单数据。
    
2. 结果集是“扁平”的（顾客信息会重复）。
    
3. MyBatis 使用 `<resultMap>` 将这些扁平的、重复的数据“折叠”成嵌套的对象结构。
    

这是**推荐的**高性能方式。

**1. Mapper 接口 (`CustomerMapper.java`)**

- (同上，我们使用已定义的 `findCustomerById_NestedResult` 方法)
    

**2. Mapper XML (`CustomerMapper.xml`)**

在 `CustomerMapper.xml` 中添加以下配置：

XML

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.yourproject.mapper.CustomerMapper">

    <resultMap id="CustomerWithOrders_NestedResult" type="com.yourproject.model.Customer">
        <id property="customerId" column="c_id" /> <result property="customerName" column="c_name" />
        <result property="phone" column="c_phone" />
        
        <collection property="orders" ofType="com.yourproject.model.Order">
            <id property="orderId" column="o_id" />
            <result property="customerId" column="c_id" /> <result property="orderTime" column="o_time" />
            <result property="totalAmount" column="o_amount" />
        </collection>
    </resultMap>

    <select id="findCustomerById_NestedResult" resultMap="CustomerWithOrders_NestedResult">
        SELECT 
            c.customer_id as c_id,
            c.customer_name as c_name,
            c.phone as c_phone,
            o.order_id as o_id,
            o.order_time as o_time,
            o.total_amount as o_amount
        FROM 
            customer c
        LEFT JOIN 
            orders o ON c.customer_id = o.customer_id
        WHERE 
            c.customer_id = #{id}
    </select>
    
</mapper>
```

**总结 (Demo 2)**

- **优点**：**性能极高**。无论查询多少顾客，永远只执行**一次** SQL。
    
- **缺点**：
    
    1. SQL 语句相对复杂（需要 `JOIN` 和使用别名）。
        
    2. `<resultMap>` 配置更复杂。
        
    3. 数据冗余：如果一个顾客有 100 个订单，那么顾客信息（姓名、电话）会在结果集中重复 100 次，会轻微增加网络传输负担。
        

### 结论与建议

|**方法**|**性能**|**SQL 复杂度**|**MyBatis配置**|**适用场景**|
|---|---|---|---|---|
|**嵌套查询 (Demo 1)**|差 (N+1)|简单|简单|1 对 1 关系，或开启了懒加载 (Lazy Loading) 且不总是需要子数据。|
|**嵌套结果 (Demo 2)**|**优秀 (1 SQL)**|复杂 (JOIN)|复杂|**强烈推荐**。绝大多数 1 对多和多对多的查询场景。|

在您的外卖项目中，查询“顾客及其所有订单”或“订单及其所有菜品”时，**请优先使用 Demo 2 (嵌套结果) 的方法**。