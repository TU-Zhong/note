当前代码中，删除Redis缓存后，后续同样的键会被更新是因为缓存的工作机制。

在`update`方法中，您先更新数据库，然后删除Redis缓存：
```java
ShopServiceImpl.java

src/main/java/com/hmdp/service/impl

Copy

public Result update(Shop shop) {

    // ...

    //先更新数据库

    updateById(shop);

    //再删除缓存

    stringRedisTemplate.delete(RedisConstants.

    CACHE_SHOP_KEY+id);

    return Result.ok();

}
```
当缓存被删除后，下一次有人调用`queryById`方法查询同一个商铺时，会发生以下流程：

1. 先尝试从Redis中获取数据，但因为缓存已被删除，所以获取不到
2. 然后从数据库中查询最新数据
3. 将查询到的数据重新写入Redis缓存

这就是为什么同样的键会被再次更新到Redis中 - 这是一种"缓存删除+懒加载"的策略，确保缓存中的数据与数据库保持一致。