好的，封装一个 Redis 工具类（通常基于 `StringRedisTemplate` 或 `RedisTemplate`）是非常常见的做法，这样可以：

1. **简化常用操作**：避免在业务代码中重复编写模板化的 Redis 调用代码。
2. **统一处理序列化/反序列化**：特别是当存储对象时，可以集中处理对象与 JSON 字符串（或其他格式）之间的转换。
3. **统一处理 Key 的前缀**：方便管理和隔离不同业务的 Key。
4. **统一处理过期时间**：为缓存设置默认或灵活的过期策略。
5. **增强代码可读性和可维护性**。
6. **封装特定业务逻辑**：例如分布式锁、限流器、ID 生成器等。

以下是一些你可以考虑封装到 Redis 工具类中的常用方法和功能，主要以 `StringRedisTemplate` 配合 JSON 序列化（例如使用 Jackson、Gson 或 FastJSON）为例：

**一、通用 Key 操作封装**

Java

```java
// 假设你已经注入了 StringRedisTemplate 和 ObjectMapper (例如 Jackson)
// private final StringRedisTemplate stringRedisTemplate;
// private final ObjectMapper objectMapper; // 用于JSON序列化

/**
 * 删除单个 key
 * @param key Redis键
 * @return 是否删除成功
 */
public Boolean delete(String key) {
    return stringRedisTemplate.delete(key);
}

/**
 * 批量删除 key
 * @param keys Redis键集合
 * @return 删除的key数量
 */
public Long delete(Collection<String> keys) {
    return stringRedisTemplate.delete(keys);
}

/**
 * 判断 key 是否存在
 * @param key Redis键
 * @return 是否存在
 */
public Boolean exists(String key) {
    return stringRedisTemplate.hasKey(key);
}

/**
 * 设置 key 的过期时间
 * @param key Redis键
 * @param timeout 过期时长
 * @param unit 时间单位
 * @return 是否设置成功
 */
public Boolean expire(String key, long timeout, TimeUnit unit) {
    return stringRedisTemplate.expire(key, timeout, unit);
}

/**
 * 获取 key 的剩余过期时间
 * @param key Redis键
 * @param unit 时间单位
 * @return 剩余过期时长，如果key不存在或没有设置过期时间，则返回-1或-2（取决于版本和具体情况）
 */
public Long getExpire(String key, TimeUnit unit) {
    return stringRedisTemplate.getExpire(key, unit);
}

/**
 * 移除 key 的过期时间，使其永久有效
 * @param key Redis键
 * @return 是否成功
 */
public Boolean persist(String key) {
    return stringRedisTemplate.persist(key);
}

// 你还可以加入 key 前缀管理，例如：
// private String buildKey(String originalKey) {
//     return "myapp:module:" + originalKey;
// }
// 然后在所有操作 key 的方法内部调用 buildKey(key)
```

**二、String 类型数据操作封装 (常用于缓存对象JSON)**

Java

```java
/**
 * 设置 String 类型的值 (通常用于缓存对象)
 * @param key Redis键
 * @param value 对象值 (会自动序列化为JSON)
 * @param timeout 过期时长 (可选, <=0 则不设过期)
 * @param unit 时间单位 (可选)
 */
public <T> void setObject(String key, T value, long timeout, TimeUnit unit) {
    if (value == null) {
        return; // 或根据业务决定是否存入"null"字符串或删除key
    }
    try {
        String jsonValue = objectMapper.writeValueAsString(value);
        if (timeout > 0 && unit != null) {
            stringRedisTemplate.opsForValue().set(key, jsonValue, timeout, unit);
        } else {
            stringRedisTemplate.opsForValue().set(key, jsonValue);
        }
    } catch (JsonProcessingException e) {
        throw new RuntimeException("序列化对象失败: " + value, e);
    }
}

public <T> void setObject(String key, T value) {
    setObject(key, value, 0, null); // 默认不过期
}

/**
 * 获取 String 类型的值并反序列化为对象
 * @param key Redis键
 * @param clazz 目标对象的Class
 * @return 反序列化后的对象，如果key不存在或反序列化失败则返回null
 */
public <T> T getObject(String key, Class<T> clazz) {
    String jsonValue = stringRedisTemplate.opsForValue().get(key);
    if (jsonValue == null || jsonValue.isEmpty()) {
        return null;
    }
    try {
        return objectMapper.readValue(jsonValue, clazz);
    } catch (JsonProcessingException e) {
        // 可以记录日志，根据业务决定是否抛出异常或返回null
        logger.error("反序列化对象失败, key: {}, json: {}", key, jsonValue, e);
        return null;
    }
}

/**
 * 获取 String 类型的值
 * @param key
 * @return
 */
public String getString(String key) {
    return stringRedisTemplate.opsForValue().get(key);
}

/**
 * 设置 String 类型的值
 * @param key
 * @param value
 */
public void setString(String key, String value) {
    stringRedisTemplate.opsForValue().set(key, value);
}

public void setString(String key, String value, long timeout, TimeUnit unit) {
    stringRedisTemplate.opsForValue().set(key, value, timeout, unit);
}

/**
 * 实现原子性递增
 * @param key Redis键
 * @param delta 增长步长 (必须 > 0)
 * @return 增长后的值
 */
public Long increment(String key, long delta) {
    if (delta < 0) {
        throw new IllegalArgumentException("递增因子必须大于0");
    }
    return stringRedisTemplate.opsForValue().increment(key, delta);
}

public Long increment(String key) {
    return increment(key, 1L);
}

/**
 * 实现原子性递减
 * @param key Redis键
 * @param delta 减少步长 (必须 > 0)
 * @return 减少后的值
 */
public Long decrement(String key, long delta) {
    if (delta < 0) {
        throw new IllegalArgumentException("递减因子必须大于0");
    }
    return stringRedisTemplate.opsForValue().decrement(key, delta);
}

public Long decrement(String key) {
    return decrement(key, 1L);
}
```

**三、Hash 类型数据操作封装**

Java

```java
/**
 * 向 Hash 中存入单个字段和值
 * @param key Redis键
 * @param hashKey Hash的字段
 * @param value 值 (会自动序列化)
 */
public <T> void hashSet(String key, String hashKey, T value) {
    try {
        stringRedisTemplate.opsForHash().put(key, hashKey, objectMapper.writeValueAsString(value));
    } catch (JsonProcessingException e) {
        throw new RuntimeException("序列化Hash值失败", e);
    }
}

/**
 * 从 Hash 中获取单个字段的值并反序列化
 * @param key Redis键
 * @param hashKey Hash的字段
 * @param clazz 目标对象的Class
 * @return 反序列化后的对象
 */
public <T> T hashGet(String key, String hashKey, Class<T> clazz) {
    Object value = stringRedisTemplate.opsForHash().get(key, hashKey);
    if (value == null) {
        return null;
    }
    try {
        return objectMapper.readValue(value.toString(), clazz);
    } catch (JsonProcessingException e) {
        logger.error("反序列化Hash值失败, key: {}, hashKey: {}", key, hashKey, e);
        return null;
    }
}

/**
 * 从 Hash 中获取所有字段和值
 * @param key Redis键
 * @param valueClazz Hash中值的目标对象Class (用于反序列化)
 * @return Map<String, T>
 */
public <T> Map<String, T> hashGetAll(String key, Class<T> valueClazz) {
    Map<Object, Object> rawMap = stringRedisTemplate.opsForHash().entries(key);
    Map<String, T> resultMap = new HashMap<>();
    try {
        for (Map.Entry<Object, Object> entry : rawMap.entrySet()) {
            resultMap.put(entry.getKey().toString(),
                          objectMapper.readValue(entry.getValue().toString(), valueClazz));
        }
    } catch (JsonProcessingException e) {
        logger.error("反序列化Hash所有值失败, key: {}", key, e);
        return Collections.emptyMap(); // 或抛出异常
    }
    return resultMap;
}

/**
 * 删除 Hash 中的一个或多个字段
 * @param key Redis键
 * @param hashKeys Hash的字段 (可变参数)
 * @return 删除的字段数量
 */
public Long hashDelete(String key, Object... hashKeys) {
    return stringRedisTemplate.opsForHash().delete(key, hashKeys);
}

/**
 * 判断 Hash 中是否存在某个字段
 * @param key Redis键
 * @param hashKey Hash的字段
 * @return 是否存在
 */
public Boolean hashExists(String key, String hashKey) {
    return stringRedisTemplate.opsForHash().hasKey(key, hashKey);
}
```

**四、List 类型数据操作封装**

Java

```java
/**
 * 从列表左侧（头部）推入一个或多个值
 * @param key Redis键
 * @param values 值 (可变参数，会自动序列化)
 * @return 推入后列表的长度
 */
public <T> Long listLeftPushAll(String key, T... values) {
    if (values == null || values.length == 0) return 0L;
    try {
        String[] stringValues = new String[values.length];
        for (int i = 0; i < values.length; i++) {
            stringValues[i] = objectMapper.writeValueAsString(values[i]);
        }
        return stringRedisTemplate.opsForList().leftPushAll(key, stringValues);
    } catch (JsonProcessingException e) {
        throw new RuntimeException("序列化List值失败", e);
    }
}

public <T> Long listLeftPush(String key, T value) {
    return listLeftPushAll(key, value);
}

/**
 * 从列表右侧（尾部）弹出一个值并反序列化
 * @param key Redis键
 * @param clazz 目标对象的Class
 * @return 反序列化后的对象，如果列表为空则返回null
 */
public <T> T listRightPop(String key, Class<T> clazz) {
    String jsonValue = stringRedisTemplate.opsForList().rightPop(key);
    if (jsonValue == null) return null;
    try {
        return objectMapper.readValue(jsonValue, clazz);
    } catch (JsonProcessingException e) {
        logger.error("反序列化List值失败, key: {}", key, e);
        return null;
    }
}

/**
 * 获取列表指定范围内的元素
 * @param key Redis键
 * @param start 开始索引 (0表示第一个)
 * @param end 结束索引 (-1表示最后一个)
 * @param clazz 目标对象的Class
 * @return 反序列化后的对象列表
 */
public <T> List<T> listRange(String key, long start, long end, Class<T> clazz) {
    List<String> jsonValues = stringRedisTemplate.opsForList().range(key, start, end);
    if (jsonValues == null || jsonValues.isEmpty()) {
        return Collections.emptyList();
    }
    List<T> results = new ArrayList<>(jsonValues.size());
    try {
        for (String jsonValue : jsonValues) {
            results.add(objectMapper.readValue(jsonValue, clazz));
        }
    } catch (JsonProcessingException e) {
        logger.error("反序列化List范围值失败, key: {}", key, e);
        return Collections.emptyList(); // 或抛出异常
    }
    return results;
}

/**
 * 获取列表长度
 * @param key Redis键
 * @return 列表长度
 */
public Long listSize(String key) {
    return stringRedisTemplate.opsForList().size(key);
}
```

**五、Set 类型数据操作封装**

Java

```java
/**
 * 向 Set 中添加一个或多个成员
 * @param key Redis键
 * @param members 成员 (可变参数，会自动序列化)
 * @return 成功添加的成员数量
 */
public <T> Long setAdd(String key, T... members) {
    if (members == null || members.length == 0) return 0L;
    try {
        String[] stringMembers = new String[members.length];
        for (int i = 0; i < members.length; i++) {
            stringMembers[i] = objectMapper.writeValueAsString(members[i]);
        }
        return stringRedisTemplate.opsForSet().add(key, stringMembers);
    } catch (JsonProcessingException e) {
        throw new RuntimeException("序列化Set成员失败", e);
    }
}

/**
 * 获取 Set 中的所有成员并反序列化
 * @param key Redis键
 * @param clazz 目标对象的Class
 * @return 反序列化后的成员集合
 */
public <T> Set<T> setMembers(String key, Class<T> clazz) {
    Set<String> jsonMembers = stringRedisTemplate.opsForSet().members(key);
    if (jsonMembers == null || jsonMembers.isEmpty()) {
        return Collections.emptySet();
    }
    Set<T> results = new HashSet<>(jsonMembers.size());
    try {
        for (String jsonMember : jsonMembers) {
            results.add(objectMapper.readValue(jsonMember, clazz));
        }
    } catch (JsonProcessingException e) {
        logger.error("反序列化Set成员失败, key: {}", key, e);
        return Collections.emptySet(); // 或抛出异常
    }
    return results;
}

/**
 * 判断成员是否在 Set 中
 * @param key Redis键
 * @param member 成员 (会自动序列化)
 * @return 是否存在
 */
public <T> Boolean setIsMember(String key, T member) {
    try {
        return stringRedisTemplate.opsForSet().isMember(key, objectMapper.writeValueAsString(member));
    } catch (JsonProcessingException e) {
        throw new RuntimeException("序列化Set成员失败", e);
    }
}

/**
 * 获取 Set 的大小
 * @param key
 * @return
 */
public Long setSize(String key) {
    return stringRedisTemplate.opsForSet().size(key);
}
```

**六、Sorted Set (ZSet) 类型数据操作封装** (与Set类似，但多了score)

**七、分布式锁封装** (如你之前的例子，但可以做得更健壮)

Java

```java
/**
 * 尝试获取分布式锁 (简单版本)
 * @param lockKey 锁的键
 * @param requestId 请求ID/锁的值 (用于保证释放锁的原子性和安全性)
 * @param expireTime 锁的过期时间 (毫秒)
 * @return 是否获取成功
 */
public Boolean tryLock(String lockKey, String requestId, long expireTime) {
    return stringRedisTemplate.opsForValue()
            .setIfAbsent(lockKey, requestId, expireTime, TimeUnit.MILLISECONDS);
}

/**
 * 释放分布式锁 (需要确保原子性，通常配合Lua脚本)
 * @param lockKey 锁的键
 * @param requestId 请求ID/锁的值
 * @return 是否释放成功
 */
public Boolean unlock(String lockKey, String requestId) {
    // 简单的非原子性释放 (不推荐用于生产环境，除非能容忍极小概率的误删)
    // String currentValue = stringRedisTemplate.opsForValue().get(lockKey);
    // if (requestId.equals(currentValue)) {
    //     return stringRedisTemplate.delete(lockKey);
    // }
    // return false;

    // 推荐使用 Lua 脚本保证原子性
    String script = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";
    Long result = stringRedisTemplate.execute(
            new DefaultRedisScript<>(script, Long.class),
            Collections.singletonList(lockKey),
            requestId
    );
    return Long.valueOf(1L).equals(result);
}
```

**八、缓存读取模式封装（例如 Cache-Aside）**

Java

```java
/**
 * 尝试从缓存获取数据，如果缓存未命中，则从数据源(supplier)获取数据，
 * 并将数据存入缓存后再返回。
 *
 * @param key         缓存键
 * @param clazz       返回数据的类型
 * @param timeout     缓存过期时间
 * @param unit        时间单位
 * @param dbSupplier  数据源获取逻辑 (例如：() -> userDao.findById(id))
 * @param <T>         数据类型
 * @return 数据
 */
public <T> T getWithCacheAside(String key, Class<T> clazz, long timeout, TimeUnit unit, Supplier<T> dbSupplier) {
    T cachedData = getObject(key, clazz);
    if (cachedData != null) {
        return cachedData; // 缓存命中
    }

    // 缓存未命中，从数据源获取
    T dbData = dbSupplier.get();
    if (dbData != null) {
        // 存入缓存
        setObject(key, dbData, timeout, unit);
    }
    // 注意：这里可能需要处理缓存穿透问题，例如存入一个短期的空值标记
    // 如果 dbData 为 null，也可以根据业务需求决定是否缓存一个特殊标记值
    return dbData;
}
```

**封装时需要考虑：**

- **日志**：在关键操作（如序列化失败、Redis连接异常等）处添加日志。
- **异常处理**：决定是向上抛出自定义异常还是返回默认值/null。
- **`ObjectMapper`的配置**：例如日期格式、是否忽略未知属性等，最好全局统一配置并注入。
- **`RedisTemplate<String, Object>` 与 `StringRedisTemplate` 的选择**：
    - `StringRedisTemplate` 默认使用 `StringRedisSerializer`，适合存取字符串或JSON字符串。
    - `RedisTemplate<String, Object>` 可以配置不同的 `ValueSerializer`（如 `Jackson2JsonRedisSerializer`, `GenericJackson2JsonRedisSerializer`）来直接存取Java对象，有时更方便，但要注意序列化器配置和潜在的类型转换问题。

选择封装哪些方法取决于你的具体业务需求，但以上列出的都是非常常见的候选。