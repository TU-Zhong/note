这个错误是因为你在 Redis 的 SET 命令中使用了不正确的语法格式。

Redis 的 SET 命令基本语法是：
```
SET key value [EX seconds] [PX milliseconds] [NX|XX]
```

你的命令 `set address beijing 5000` 看起来是想设置一个键 "address" 值为 "beijing" 并设置过期时间 5000 秒，但正确的写法应该是：

```
127.0.0.1:6379> SET address beijing EX 5000
```

或者如果你想要毫秒级过期时间：
```
127.0.0.1:6379> SET address beijing PX 5000
```

错误原因：
1. 你没有指定过期时间参数（EX 或 PX）
2. Redis 将 "5000" 解释为 value 的一部分而不是过期时间，但因为语法不符合 SET 命令的规范而报错

其他正确用法示例：
- 简单的键值设置：`SET address beijing`
- 设置键并指定秒级过期：`SET address beijing EX 10`
- 设置键并指定毫秒级过期：`SET address beijing PX 10000`
- 只有在键不存在时设置：`SET address beijing NX`
- 只有在键已存在时设置：`SET address beijing XX`