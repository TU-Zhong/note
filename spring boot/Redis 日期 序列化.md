这个错误是由于Jackson序列化器无法正确处理

`DishVO`

对象中的

`updateTime`

字段。这通常发生在使用Redis缓存对象时，如果对象中包含了不支持默认序列化的类型（如`LocalDateTime`）。

### 解决方案

1. 首先，修改Redis配置，使用Jackson2JsonRedisSerializer并配置ObjectMapper来处理Java 8日期时间类型：
```java

RedisConfiguration.java

sky-server/src/main/java/com/sky/config

Apply

import com.fasterxml.jackson.databind.jsontype.impl.

LaissezFaireSubTypeValidator;

import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;

import org.springframework.context.annotation.Bean;

import org.springframework.context.annotation.Configuration;

import org.springframework.data.redis.connection.RedisConnectionFactory;

import org.springframework.data.redis.core.RedisTemplate;

import org.springframework.data.redis.serializer.

Jackson2JsonRedisSerializer;

import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration

public class RedisConfiguration {

    @Bean

    public RedisTemplate<String, Object> redisTemplate

    (RedisConnectionFactory connectionFactory) {

        RedisTemplate<String, Object> redisTemplate = new 

        RedisTemplate<>();

        redisTemplate.setConnectionFactory(connectionFactory);

        // 使用Jackson2JsonRedisSerializer替换默认序列化

        Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer 

        = new Jackson2JsonRedisSerializer<>(Object.class);

        // 配置ObjectMapper

        ObjectMapper objectMapper = new ObjectMapper();

        // 指定要序列化的域，field,get和set,以及修饰符范围，ANY是都有包括

        private和public

        objectMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.

        Visibility.ANY);

        // 解决LocalDateTime序列化问题

        objectMapper.disable(SerializationFeature.

        WRITE_DATES_AS_TIMESTAMPS);

        objectMapper.registerModule(new JavaTimeModule());

        // 指定序列化输入的类型，类必须是非final修饰的，final修饰的类，比如

        String,Integer等会抛出异常

        objectMapper.activateDefaultTyping(LaissezFaireSubTypeValidator.

        instance, 

                                          ObjectMapper.DefaultTyping.

                                          NON_FINAL);

        jackson2JsonRedisSerializer.setObjectMapper(objectMapper);

        // 设置key和value的序列化规则

        redisTemplate.setKeySerializer(new StringRedisSerializer());

        redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);

        // 设置hash key和value的序列化规则

        redisTemplate.setHashKeySerializer(new StringRedisSerializer());

        redisTemplate.setHashValueSerializer

        (jackson2JsonRedisSerializer);

        redisTemplate.afterPropertiesSet();

        return redisTemplate;

    }

}
```
2. 确保
    
     `DishVO`
    
    类中的所有字段都是可序列化的，特别是
    
     `updateTime`
    
    字段：
```java


DishVO.java

sky-pojo/src/main/java/com/sky/vo

Apply

package com.sky.vo;

import com.fasterxml.jackson.annotation.JsonFormat;

import com.fasterxml.jackson.databind.annotation.JsonDeserialize;

import com.fasterxml.jackson.databind.annotation.JsonSerialize;

import com.fasterxml.jackson.datatype.jsr310.deser.

LocalDateTimeDeserializer;

import com.fasterxml.jackson.datatype.jsr310.ser.

LocalDateTimeSerializer;

import com.sky.entity.DishFlavor;

import lombok.AllArgsConstructor;

import lombok.Builder;

import lombok.Data;

import lombok.NoArgsConstructor;

import java.io.Serializable;

import java.math.BigDecimal;

import java.time.LocalDateTime;

import java.util.ArrayList;

import java.util.List;

@Data

@Builder

@NoArgsConstructor

@AllArgsConstructor

public class DishVO implements Serializable {

    private Long id;

    //菜品名称

    private String name;

    //菜品分类id

    private Long categoryId;

    //菜品价格

    private BigDecimal price;

    //图片

    private String image;

    //描述信息

    private String description;

    //0 停售 1 起售

    private Integer status;

    //更新时间 - 添加Jackson注解处理LocalDateTime

    @JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")

    @JsonSerialize(using = LocalDateTimeSerializer.class)

    @JsonDeserialize(using = LocalDateTimeDeserializer.class)

    private LocalDateTime updateTime;

    //分类名称

    private String categoryName;

    //菜品关联的口味

    private List<DishFlavor> flavors = new ArrayList<>();

    //private Integer copies;

}
```
3. 确保项目中包含了Jackson JSR310模块的依赖，在
    
     `pom.xml`
    
    中添加：
```java


pom.xml

sky-pojo

Apply

<dependency>

    <groupId>com.fasterxml.jackson.datatype</groupId>

    <artifactId>jackson-datatype-jsr310</artifactId>

    <version>2.13.3</version>

</dependency>

4. 如果您使用的是Spring Boot，可以在
    
     `application.yml`
    
    中添加Jackson日期格式配置：

application.yml

sky-server/src/main/resources

Apply

spring:

  jackson:

    date-format: yyyy-MM-dd HH:mm:ss

    time-zone: GMT+8

    serialization:

      write-dates-as-timestamps: false

5. 修改
    
     `DishServiceImpl`
    
    中的缓存处理逻辑，确保在缓存前后正确处理对象：

DishServiceImpl.java

sky-server/src/main/java/com/sky/service/impl

Apply

@Override

public DishVO findDishWithFlavor(Integer id) {

    // 设置key

    String key = DISH_KEY + id;

    // 看Redis中是否已经有了当前key的值

    DishVO dishVO = null;

    try {

        dishVO = (DishVO) redisTemplate.opsForValue().get(key);

    } catch (Exception e) {

        log.error("从Redis获取菜品数据失败: {}", e.getMessage());

        // 发生异常时，删除可能损坏的缓存

        redisTemplate.delete(key);

    }

    // 有就直接返回缓存中的值

    if (dishVO != null) {

        log.info("从缓存中获取菜品数据, id: {}", id);

        return dishVO;

    }

    // 没有就去数据库中查询值

    log.info("从数据库中获取菜品数据, id: {}", id);

    dishVO = dishMapper.findDishByIdWithFlavor(id);

    if (dishVO != null) {

        try {

            // 写入redis中

            redisTemplate.opsForValue().set(key, dishVO, 

            CACHE_EXPIRE_TIME, TimeUnit.HOURS);

            log.info("菜品数据已缓存到Redis, id: {}", id);

        } catch (Exception e) {

            log.error("缓存菜品数据到Redis失败: {}", e.getMessage());

        }

    }

    return dishVO;

}
```
### 其他可能的解决方案

如果上述方法仍然不能解决问题，您可以尝试以下替代方案：

1. **使用自定义序列化器**：
    - 为
        
         `DishVO`
        
        类创建自定义的序列化器和反序列化器
2. **使用String类型缓存**：
    - 将对象转换为JSON字符串后再缓存
    - 从缓存获取时再将JSON字符串转回对象
```java


DishServiceImpl.java

sky-server/src/main/java/com/sky/service/impl

Apply

// 使用String类型缓存的替代方法

@Override

public DishVO findDishWithFlavor(Integer id) {

    // 设置key

    String key = DISH_KEY + id;

    // 从Redis中获取JSON字符串

    String dishJson = (String) redisTemplate.opsForValue().get(key);

    // 如果缓存中存在

    if (dishJson != null && !dishJson.isEmpty()) {

        try {

            // 将JSON字符串转换回对象

            ObjectMapper mapper = new ObjectMapper();

            mapper.registerModule(new JavaTimeModule());

            mapper.disable(SerializationFeature.

            WRITE_DATES_AS_TIMESTAMPS);

            DishVO dishVO = mapper.readValue(dishJson, DishVO.class);

            log.info("从缓存中获取菜品数据, id: {}", id);

            return dishVO;

        } catch (Exception e) {

            log.error("解析缓存的JSON数据失败: {}", e.getMessage());

            // 删除可能损坏的缓存

            redisTemplate.delete(key);

        }

    }

    // 从数据库中查询

    log.info("从数据库中获取菜品数据, id: {}", id);

    DishVO dishVO = dishMapper.findDishByIdWithFlavor(id);

    if (dishVO != null) {

        try {

            // 将对象转换为JSON字符串

            ObjectMapper mapper = new ObjectMapper();

            mapper.registerModule(new JavaTimeModule());

            mapper.disable(SerializationFeature.

            WRITE_DATES_AS_TIMESTAMPS);

            String json = mapper.writeValueAsString(dishVO);

            // 将JSON字符串缓存到Redis

            redisTemplate.opsForValue().set(key, json, 

            CACHE_EXPIRE_TIME, TimeUnit.HOURS);

            log.info("菜品数据已缓存到Redis, id: {}", id);

        } catch (Exception e) {

            log.error("缓存菜品数据到Redis失败: {}", e.getMessage());

        }

    }

    return dishVO;

}
```
通过以上修改，您应该能够解决`LocalDateTime`序列化的问题。这个问题在使用Redis缓存Java对象时很常见，特别是当对象包含Java 8日期时间类型时