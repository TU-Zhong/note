好的，没问题。这是一份为你准备的、关于 Redis 的 60 道核心面试题。

这份题单从 Redis 的基础理论出发，逐步深入到持久化、高可用架构，并重点结合了如何在 Spring Boot 项目中进行各种场景的实战应用，旨在帮你全面掌握面试所需的 Redis 知识。

---

### **Redis 核心面试题 60 问 (结合 Spring Boot)**

#### **第一部分：Redis 核心基础与数据结构 (15题)**

这部分主要考察你对 Redis 基础概念和核心数据结构的理解。

1. **什么是 Redis？它有哪些优点和应用场景？**
    
    - 请简述你对 Redis 的理解。
        
    - 相比于其他键值数据库（如 Memcached），它有哪些优势？
        
    - 你在项目中通常用 Redis 来做什么？（例如：缓存、分布式锁、计数器等）
        
2. **Redis 为什么这么快？**
    
    - 请从数据结构、内存存储和 I/O 模型（多路复用）三个方面来解释。
        
3. **Redis 的单线程模型是什么意思？**
    
    - 为什么 Redis 选择单线程模型处理网络请求？
        
    - 单线程模型有什么优缺点？
        
4. **Redis 有哪些基本数据类型？**
    
    - 请列出并简述 String, List, Hash, Set, Sorted Set (ZSet) 这五种基本数据类型。
        
5. **String 类型的使用场景？**
    
    - 在 Spring Boot 中，你如何使用 `StringRedisTemplate` 来缓存一个用户信息（JSON 字符串）？
        
    - 如何使用 `opsForValue().increment()` 实现一个全局递增ID或文章浏览量计数器？
        
6. **List 类型的使用场景？**
    
    - 如何使用 List 类型实现一个简单的消息队列（生产者/消费者模型）？
        
    - 在 Spring Boot 中，你会分别使用哪个方法来实现 `LPUSH` 和 `BRPOP`？
        
7. **Hash 类型的使用场景？**
    
    - 为什么要用 Hash 类型来存储一个对象的多个字段，而不是用多个 String 类型的 Key？（例如：存储用户信息）
        
    - 在 Spring Boot 中，如何使用 `opsForHash().putAll()` 和 `opsForHash().entries()` 来操作一个 Hash？
        
8. **Set 类型的使用场景？**
    
    - 如何使用 Set 类型实现共同关注、共同好友的功能？
        
    - 在 Spring Boot 中，如何使用 `opsForSet().add()` 添加元素，以及如何使用 `opsForSet().intersect()` 获取交集？
        
9. **Sorted Set (ZSet) 类型的使用场景？**
    
    - 如何使用 ZSet 实现一个排行榜，例如“本周热销商品排行榜”？
        
    - 在 Spring Boot 中，如何使用 `opsForZSet().add()` 添加成员和分数，以及如何使用 `opsForZSet().reverseRangeWithScores()` 获取排名靠前的成员？
        
10. **Redis 的键（Key）过期策略是怎样的？**
    
    - Redis 是如何判断一个 Key 是否过期的？（定期删除 + 惰性删除）
        
    - 在 Spring Boot 中，使用 `redisTemplate.expire()` 方法设置过期时间时需要注意什么？
        
11. **除了基本数据类型，你还了解 Redis 的哪些高级数据结构？**
    
    - 简述一下 HyperLogLog, Bitmap, Geospatial 和 Stream 的作用和应用场景。
        
12. **HyperLogLog 是用来做什么的？**
    
    - 如果要统计一个网站一天内的独立访客数（UV），你会如何设计？
        
13. **Bitmap 是用来做什么的？**
    
    - 如果要实现用户每日签到功能，并统计用户连续签到天数，你会如何使用 Bitmap 设计？
        
14. **Geospatial 是用来做什么的？**
    
    - 如何使用 Redis 实现“附近的人”或“附近的门店”的功能？
        
15. **什么是 Big Key（大键）？它有什么危害？如何发现和处理？**
    
    - 在你的项目中，是如何避免产生 Big Key 的？
        

---

#### **第二部分：Redis 进阶、持久化与事务 (15题)**

这部分考察对 Redis 运行机制和数据安全性的理解。

16. **Redis 的持久化机制有哪几种？它们有什么区别？**
    
    - 请详细对比 RDB（快照）和 AOF（只追加文件）的工作原理、优缺点。
        
17. **RDB 是如何工作的？触发 RDB 的方式有哪些？**
    
    - 描述一下 `SAVE` 和 `BGSAVE` 命令的区别。
        
18. **AOF 是如何工作的？AOF 的重写（Rewrite）机制是什么？**
    
    - AOF 文件越来越大怎么办？AOF 重写的作用是什么？它是在哪个线程中执行的？
        
19. **在项目中，如何选择合适的持久化方式？**
    
    - 如果数据可以丢失一部分，但要求恢复速度快，该选哪个？
        
    - 如果要求数据尽可能不丢失，该选哪个？
        
    - 可以在一台 Redis 服务器上同时开启 RDB 和 AOF 吗？如果可以，Redis 重启时会加载哪个文件？
        
20. **什么是 Redis 的内存淘汰策略？**
    
    - 当 Redis 内存不足时，会发生什么？
        
    - 请列举并解释几种常见的淘汰策略（例如 `volatile-lru`, `allkeys-lru`, `volatile-lfu`, `noeviction`）。
        
21. **什么是 Redis 的事务？它能保证原子性吗？**
    
    - 描述一下 `MULTI`, `EXEC`, `DISCARD` 命令的作用。
        
    - 为什么说 Redis 的事务是“部分原子性”的？如果事务中的某个命令执行失败，会发生什么？
        
22. **Redis 事务中的 `WATCH` 命令是做什么的？**
    
    - 如何使用 `WATCH` 实现一个简单的乐观锁？
        
23. **什么是 Pipeline（管道）？它和事务有什么区别？**
    
    - 为什么使用 Pipeline 可以提升性能？
        
    - 在 Spring Boot 中，如何通过 `redisTemplate.executePipelined()` 来执行一批命令？
        
24. **Redis 的发布/订阅（Pub/Sub）功能是如何工作的？**
    
    - 你在什么场景下会使用它？
        
    - 在 Spring Boot 中，如何实现一个消息的发布者和监听器？
        
25. **什么是 Lua 脚本？在 Redis 中使用它有什么好处？**
    
    - 为什么说 Lua 脚本的执行是原子性的？
        
    - 在 Spring Boot 中，如何使用 `DefaultRedisScript` 来执行一段 Lua 脚本？
        
26. **如何使用 Redis 实现一个分布式锁？**
    
    - 最简单的实现方式是什么？（`SETNX`）
        
    - `SETNX` + `EXPIRE` 的两步操作有什么问题？如何解决？（使用 `SET key value EX seconds NX` 命令）
        
27. **实现分布式锁时，需要考虑哪些问题？**
    
    - 锁的超时释放与业务执行时间的关系是怎样的？
        
    - 如何实现一个可重入的分布式锁？
        
    - 什么是 Redlock（红锁）算法？
        
28. **在 Spring Boot 项目中，你通常使用哪个库来实现分布式锁？**
    
    - 简述一下 Redisson 的工作原理。
        
29. **Redis 6.0 之后有哪些重要的新特性？**
    
    - 简述多线程 I/O、客户端缓存（Client Side Caching）、ACLs 等。
        
30. **如何优雅地设计 Redis 的 Key？**
    
    - 请给出一个你认为比较好的 Key 命名规范（例如 `项目名:业务名:唯一标识`）。
        

---

#### **第三部分：Spring Boot 集成与核心应用场景 (20题)**

这部分是面试的重中之重，考察将 Redis 理论应用于实际项目开发的能力。

31. **Spring Boot 项目如何集成 Redis？**
    
    - 需要引入哪个 starter 依赖？
        
    - 在 `application.yml` 中需要配置哪些核心参数？
        
32. **`RedisTemplate` 和 `StringRedisTemplate` 有什么区别？**
    
    - 它们默认的序列化器是什么？
        
    - 在项目中，你为什么通常会选择 `StringRedisTemplate` 或者将 `RedisTemplate` 的序列化器配置为 JSON？
        
33. **如何自定义 `RedisTemplate` 的序列化方式？**
    
    - 请编写一个配置类，将 `RedisTemplate` 的 Key 序列化器设置为 `StringRedisSerializer`，Value 序列化器设置为 `Jackson2JsonRedisSerializer`。
        
34. **Spring Cache 是什么？如何在 Spring Boot 中启用它？**
    
    - `@EnableCaching` 注解的作用是什么？
        
    - 如何在配置文件中指定使用 Redis 作为缓存管理器？
        
35. **`@Cacheable` 注解是如何工作的？**
    
    - 在一个查询方法上（如 `findUserById`）使用 `@Cacheable`，请解释其执行流程。
        
    - 如何自定义缓存的 Key？（使用 SpEL 表达式）
        
36. **`@CachePut` 和 `@CacheEvict` 注解分别用于什么场景？**
    
    - 当更新用户信息后，如何确保缓存也被同步更新？
        
    - 当删除用户后，如何确保缓存被清除？
        
37. **如何为 Spring Cache 设置统一的过期时间（TTL）？如何为特定的缓存设置不同的过期时间？**
    
    - 请给出在 `application.yml` 中的配置示例。
        
38. **什么是缓存穿透？有哪些解决方案？**
    
    - 请解释缓存空对象和布隆过滤器（Bloom Filter）这两种方法的原理。
        
39. **什么是缓存击穿（热点 Key 失效）？有哪些解决方案？**
    
    - 请解释使用互斥锁（Mutex Lock）或逻辑过期这两种方法的原理。
        
40. **什么是缓存雪崩？有哪些解决方案？**
    
    - 请解释设置随机过期时间、多级缓存、服务熔断与降级等策略。
        
41. **如何使用 Spring Session 和 Redis 实现分布式 Session 共享？**
    
    - 需要引入哪个依赖？
        
    - 需要在配置文件中做什么修改？
        
    - 它的实现原理是什么？
        
42. **请设计一个基于 Redis 的 API 接口限流方案。**
    
    - 你会选择哪种数据结构？（例如 String 或 ZSet）
        
    - 如何实现一个基于滑动窗口的限流算法？
        
43. **请设计一个用户“点赞”功能的后端实现。**
    
    - 如何使用 Redis 存储每个帖子的点赞用户集合？
        
    - 如何高效地统计点赞总数？
        
    - 如何判断某个用户是否已经点赞？
        
44. **在 Spring Boot 中，如何实现 Redis 的批量操作以提升性能？**
    
    - 请说明 Pipeline 和 `MGET`/`MSET` 的应用场景。
        
45. **你是否在 Spring Boot 中处理过 Redis 连接池的配置？**
    
    - 常用的 Redis 客户端有哪些（Jedis, Lettuce）？Spring Boot 2.x 默认使用哪个？
        
    - Lettuce 相比 Jedis 有什么优势？
        
46. **在 `RedisTemplate` 操作中，`BoundKeyOperations` 是什么？**
    
    - 它相比直接使用 `opsForXXX()` 有什么方便之处？
        
47. **如何监听 Redis Key 的过期事件？**
    
    - 需要在 Redis 中开启什么配置？
        
    - 在 Spring Boot 中，如何编写一个 `RedisKeyExpirationListener`？
        
48. **`@Cacheable` 在什么情况下会失效？**
    
    - 例如，在同一个类中调用另一个标注了 `@Cacheable` 的方法。
        
49. **在 Spring Boot 项目中，如果 Redis 突然宕机，你的应用会发生什么？如何进行容错处理？**
    
    - 在缓存场景下，如何设计能让业务逻辑在 Redis 故障时继续执行（例如，直接查询数据库）？
        
50. **如何保证数据库和 Redis 缓存的数据一致性？**
    
    - 请讨论 Cache-Aside Pattern（旁路缓存模式）。
        
    - 讨论先更新数据库再删除缓存，和先删除缓存再更新数据库这两种方案的优缺点及可能遇到的问题。
        

---

#### **第四部分：Redis 高可用与集群架构 (10题)**

这部分考察对 Redis 生产环境部署和运维的理解。

51. **Redis 的主从复制（Master-Slave）是如何工作的？**
    
    - 首次同步（全量复制）和后续同步（增量复制）的流程是怎样的？
        
52. **主从复制有什么作用？**
    
    - 数据备份、读写分离等。
        
53. **什么是哨兵（Sentinel）模式？它的作用是什么？**
    
    - 它如何监控主从节点的状态？
        
    - 它如何实现自动故障转移（Failover）？
        
54. **哨兵模式的选举机制是怎样的？**
    
    - 一个哨兵集群至少需要几个节点才能正常工作？为什么？
        
55. **在 Spring Boot 中，如何配置连接到 Sentinel 集群？**
    
    - `application.yml` 中的配置和单机模式有何不同？
        
56. **什么是 Redis Cluster（集群）模式？它和哨兵模式有什么区别？**
    
    - Redis Cluster 是如何实现数据分片和高可用的？
        
57. **Redis Cluster 的哈希槽（Hash Slot）是什么？**
    
    - 一个 Redis Cluster 共有多少个哈希槽？
        
    - 它是如何将 Key 映射到哈希槽，再将哈希槽分配到不同节点的？
        
58. **当向 Redis Cluster 请求一个 Key 时，如果这个 Key 不在当前节点，会发生什么？**
    
    - 简述 MOVED 和 ASK 重定向的原理。
        
59. **在 Spring Boot 中，如何配置连接到 Redis Cluster？**
    
    - `application.yml` 中的配置和哨兵模式有何不同？
        
60. **在生产环境中，你会选择哨兵模式还是集群模式？为什么？**
    
    - 请从数据量、QPS、运维复杂度和扩展性等方面进行比较。