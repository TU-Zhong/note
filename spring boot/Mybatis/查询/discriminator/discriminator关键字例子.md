好的，下面是两个使用MyBatis `<discriminator>`（鉴别器）的例子，包含建表SQL、Java实体类和Mapper XML配置，希望能帮助你巩固学习。

`<discriminator>` 的主要作用是根据某列的值来决定使用哪种结果映射逻辑，通常有两种主要用途：

1. **实现类似“继承”的映射**：根据某个字段的值，将查询结果映射到父类引用的不同子类实例。
2. **条件化映射属性**：根据某个字段的值，为同一个Java对象的不同属性进行赋值。

---

### 示例1：根据宠物类型映射到不同子类 (模拟继承)

假设我们有一个 `pets` 表，存储不同类型的宠物（狗和猫），它们有一些共同的属性，也有各自特有的属性。

**1. 建表SQL (以MySQL为例)**

SQL

```
CREATE TABLE pets (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    pet_type VARCHAR(20) NOT NULL,  -- 'DOG' 或 'CAT'，作为鉴别器列
    -- Dog 特有属性
    breed VARCHAR(50),              -- 品种 (狗)
    plays_fetch BOOLEAN,            -- 是否喜欢玩叼回游戏 (狗)
    -- Cat 特有属性
    likes_catnip BOOLEAN,           -- 是否喜欢猫薄荷 (猫)
    number_of_whiskers INT          -- 胡须数量 (猫)
);

-- 插入示例数据
INSERT INTO pets (name, pet_type, breed, plays_fetch)
VALUES ('Buddy', 'DOG', 'Golden Retriever', TRUE);

INSERT INTO pets (name, pet_type, breed, plays_fetch)
VALUES ('Lucy', 'DOG', 'Poodle', FALSE);

INSERT INTO pets (name, pet_type, likes_catnip, number_of_whiskers)
VALUES ('Whiskers', 'CAT', TRUE, 24);

INSERT INTO pets (name, pet_type, likes_catnip, number_of_whiskers)
VALUES ('Shadow', 'CAT', FALSE, 20);
```

**2. Java 实体类**

- **`Pet.java` (父类)**
    
    Java
    
    ```
    package com.example.model;
    
    public abstract class Pet {
        private Integer id;
        private String name;
        private String petType; // 对应数据库的 pet_type
    
        // Constructors, Getters, Setters
        public Pet() {}
    
        public Pet(Integer id, String name, String petType) {
            this.id = id;
            this.name = name;
            this.petType = petType;
        }
    
        public Integer getId() { return id; }
        public void setId(Integer id) { this.id = id; }
        public String getName() { return name; }
        public void setName(String name) { this.name = name; }
        public String getPetType() { return petType; }
        public void setPetType(String petType) { this.petType = petType; }
    
        @Override
        public String toString() {
            return "Pet{id=" + id + ", name='" + name + "', petType='" + petType + "'}";
        }
    }
    ```
    
- **`Dog.java` (子类)**
    
    Java
    
    ```
    package com.example.model;
    
    public class Dog extends Pet {
        private String breed;
        private boolean playsFetch;
    
        // Constructors, Getters, Setters
        public Dog() {
            super();
            setPetType("DOG"); // 可以默认设置类型
        }
    
        public String getBreed() { return breed; }
        public void setBreed(String breed) { this.breed = breed; }
        public boolean isPlaysFetch() { return playsFetch; }
        public void setPlaysFetch(boolean playsFetch) { this.playsFetch = playsFetch; }
    
        @Override
        public String toString() {
            return "Dog{id=" + getId() + ", name='" + getName() + "', petType='" + getPetType() +
                   "', breed='" + breed + "', playsFetch=" + playsFetch + "}";
        }
    }
    ```
    
- **`Cat.java` (子类)**
    
    Java
    
    ```
    package com.example.model;
    
    public class Cat extends Pet {
        private boolean likesCatnip;
        private Integer numberOfWhiskers;
    
        // Constructors, Getters, Setters
        public Cat() {
            super();
            setPetType("CAT"); // 可以默认设置类型
        }
    
        public boolean isLikesCatnip() { return likesCatnip; }
        public void setLikesCatnip(boolean likesCatnip) { this.likesCatnip = likesCatnip; }
        public Integer getNumberOfWhiskers() { return numberOfWhiskers; }
        public void setNumberOfWhiskers(Integer numberOfWhiskers) { this.numberOfWhiskers = numberOfWhiskers; }
    
        @Override
        public String toString() {
            return "Cat{id=" + getId() + ", name='" + getName() + "', petType='" + getPetType() +
                   "', likesCatnip=" + likesCatnip + ", numberOfWhiskers=" + numberOfWhiskers + "}";
        }
    }
    ```
    

**3. Mapper XML (`PetMapper.xml`)**

XML

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.PetMapper">

    <resultMap id="basePetResultMap" type="com.example.model.Pet">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="petType" column="pet_type"/>
    </resultMap>

    <resultMap id="dogResultMap" type="com.example.model.Dog" extends="basePetResultMap">
        <result property="breed" column="breed"/>
        <result property="playsFetch" column="plays_fetch"/>
    </resultMap>

    <resultMap id="catResultMap" type="com.example.model.Cat" extends="basePetResultMap">
        <result property="likesCatnip" column="likes_catnip"/>
        <result property="numberOfWhiskers" column="number_of_whiskers"/>
    </resultMap>

    <resultMap id="petDiscriminatorResultMap" type="com.example.model.Pet">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="petType" column="pet_type"/> <discriminator javaType="string" column="pet_type">
            <case value="DOG" type="com.example.model.Dog">
                <result property="breed" column="breed"/>
                <result property="playsFetch" column="plays_fetch"/>
            </case>
            <case value="CAT" type="com.example.model.Cat">
                <result property="likesCatnip" column="likes_catnip"/>
                <result property="numberOfWhiskers" column="number_of_whiskers"/>
            </case>
        </discriminator>
    </resultMap>

    <select id="findAllPets" resultMap="petDiscriminatorResultMap">
        SELECT id, name, pet_type, breed, plays_fetch, likes_catnip, number_of_whiskers
        FROM pets
    </select>

    <select id="findPetById" parameterType="int" resultMap="petDiscriminatorResultMap">
        SELECT id, name, pet_type, breed, plays_fetch, likes_catnip, number_of_whiskers
        FROM pets WHERE id = #{id}
    </select>
</mapper>
```

**Mapper 接口 (`PetMapper.java`)**

Java

```
package com.example.mapper;

import com.example.model.Pet;
import java.util.List;

public interface PetMapper {
    List<Pet> findAllPets();
    Pet findPetById(int id);
}
```

**说明：**

- `petDiscriminatorResultMap` 是核心。它首先映射了 `Pet` 类的基本属性。
- `<discriminator>` 标签指定了 `javaType="string"` (因为 `pet_type` 列是字符串) 和 `column="pet_type"` (数据库中用于区分类型的列)。
- `<case>` 标签：
    - `value="DOG"`: 当 `pet_type` 的值是 "DOG" 时，MyBatis 会实例化 `com.example.model.Dog` 类型（通过 `type` 属性指定），并应用 `<case>` 内部定义的属性映射。
    - `value="CAT"`: 类似地，当值为 "CAT" 时，实例化 `com.example.model.Cat` 并映射其特有属性。
- 查询返回 `List<Pet>`，但列表中的实际对象会是 `Dog` 或 `Cat` 的实例。

---

### 示例2：根据内容类型，映射同一对象的不同属性集

假设我们有一个 `notifications` 表，存储不同类型的通知（例如：`MESSAGE`, `ALERT`, `PROMOTION`）。所有通知都映射到同一个 `Notification` 对象，但根据类型，我们可能只关心某些特定字段。

**1. 建表SQL (以MySQL为例)**

SQL

```
CREATE TABLE notifications (
    id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT NOT NULL,
    notification_type VARCHAR(20) NOT NULL, -- 'MESSAGE', 'ALERT', 'PROMOTION'
    title VARCHAR(255),
    -- MESSAGE 特有
    sender_name VARCHAR(100),
    message_preview TEXT,
    -- ALERT 特有
    severity VARCHAR(10), -- 'LOW', 'MEDIUM', 'HIGH'
    alert_details TEXT,
    -- PROMOTION 特有
    discount_code VARCHAR(30),
    expiry_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO notifications (user_id, notification_type, title, sender_name, message_preview)
VALUES (101, 'MESSAGE', 'New Message from Jane', 'Jane Doe', 'Hey, are you free tonight?...');

INSERT INTO notifications (user_id, notification_type, title, severity, alert_details)
VALUES (102, 'ALERT', 'System Maintenance Soon', 'MEDIUM', 'System will be down for maintenance on Sunday at 2 AM.');

INSERT INTO notifications (user_id, notification_type, title, discount_code, expiry_date)
VALUES (101, 'PROMOTION', 'Special Discount For You!', 'SUMMER25', '2025-08-31');
```

**2. Java 实体类 (`Notification.java`)**

Java

```
package com.example.model;

import java.util.Date;

public class Notification {
    private Integer id;
    private Integer userId;
    private String notificationType; // 鉴别器列
    private String title;
    private Date createdAt;

    // MESSAGE specific
    private String senderName;
    private String messagePreview;

    // ALERT specific
    private String severity;
    private String alertDetails;

    // PROMOTION specific
    private String discountCode;
    private Date expiryDate;

    // Getters and Setters for all fields
    public Integer getId() { return id; }
    public void setId(Integer id) { this.id = id; }
    public Integer getUserId() { return userId; }
    public void setUserId(Integer userId) { this.userId = userId; }
    public String getNotificationType() { return notificationType; }
    public void setNotificationType(String notificationType) { this.notificationType = notificationType; }
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }
    public Date getCreatedAt() { return createdAt; }
    public void setCreatedAt(Date createdAt) { this.createdAt = createdAt; }
    public String getSenderName() { return senderName; }
    public void setSenderName(String senderName) { this.senderName = senderName; }
    public String getMessagePreview() { return messagePreview; }
    public void setMessagePreview(String messagePreview) { this.messagePreview = messagePreview; }
    public String getSeverity() { return severity; }
    public void setSeverity(String severity) { this.severity = severity; }
    public String getAlertDetails() { return alertDetails; }
    public void setAlertDetails(String alertDetails) { this.alertDetails = alertDetails; }
    public String getDiscountCode() { return discountCode; }
    public void setDiscountCode(String discountCode) { this.discountCode = discountCode; }
    public Date getExpiryDate() { return expiryDate; }
    public void setExpiryDate(Date expiryDate) { this.expiryDate = expiryDate; }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        sb.append("Notification{id=").append(id)
          .append(", userId=").append(userId)
          .append(", notificationType='").append(notificationType).append('\'')
          .append(", title='").append(title).append('\'')
          .append(", createdAt=").append(createdAt);
        if (senderName != null) sb.append(", senderName='").append(senderName).append('\'');
        if (messagePreview != null) sb.append(", messagePreview='").append(messagePreview).append('\'');
        if (severity != null) sb.append(", severity='").append(severity).append('\'');
        if (alertDetails != null) sb.append(", alertDetails='").append(alertDetails).append('\'');
        if (discountCode != null) sb.append(", discountCode='").append(discountCode).append('\'');
        if (expiryDate != null) sb.append(", expiryDate=").append(expiryDate);
        sb.append('}');
        return sb.toString();
    }
}
```

**3. Mapper XML (`NotificationMapper.xml`)**

XML

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.NotificationMapper">

    <resultMap id="notificationResultMap" type="com.example.model.Notification">
        <id property="id" column="id"/>
        <result property="userId" column="user_id"/>
        <result property="notificationType" column="notification_type"/>
        <result property="title" column="title"/>
        <result property="createdAt" column="created_at"/>

        <discriminator javaType="string" column="notification_type">
            <case value="MESSAGE">
                <result property="senderName" column="sender_name"/>
                <result property="messagePreview" column="message_preview"/>
            </case>
            <case value="ALERT">
                <result property="severity" column="severity"/>
                <result property="alertDetails" column="alert_details"/>
            </case>
            <case value="PROMOTION">
                <result property="discountCode" column="discount_code"/>
                <result property="expiryDate" column="expiry_date"/>
            </case>
        </discriminator>
    </resultMap>

    <select id="findAllNotifications" resultMap="notificationResultMap">
        SELECT * FROM notifications
    </select>

    <select id="findNotificationsByUserId" parameterType="int" resultMap="notificationResultMap">
        SELECT * FROM notifications WHERE user_id = #{userId}
    </select>

</mapper>
```

**Mapper 接口 (`NotificationMapper.java`)**

Java

```
package com.example.mapper;

import com.example.model.Notification;
import java.util.List;

public interface NotificationMapper {
    List<Notification> findAllNotifications();
    List<Notification> findNotificationsByUserId(int userId);
}
```

**说明：**

- `notificationResultMap` 映射到 `com.example.model.Notification` 类。
- 公共属性（`id`, `userId`, `notificationType`, `title`, `createdAt`）被直接映射。
- `<discriminator>` 检查 `notification_type` 列的值：
    - 如果值为 "MESSAGE"，则额外映射 `senderName` 和 `messagePreview` 属性。
    - 如果值为 "ALERT"，则额外映射 `severity` 和 `alertDetails` 属性。
    - 如果值为 "PROMOTION"，则额外映射 `discountCode` 和 `expiryDate` 属性。
- 在这个例子中，`<case>` 标签内部没有 `type` 属性，因为我们总是映射到同一个 `Notification` 类，只是根据条件选择性地填充该类的不同属性。未被选中的属性在其Java对象中将保持为null或其默认值。

---

这两个例子展示了 `<discriminator>` 的不同用法。第一个例子更侧重于对象类型的选择（类似多态），第二个例子侧重于同一对象内部属性的条件化填充。根据你的具体需求选择合适的模式。记得将包名 `com.example.model` 和 `com.example.mapper` 替换成你项目中实际的包名。