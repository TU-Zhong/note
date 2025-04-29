1. windows 安装
	[5分钟在windows上安装redis_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV17D4y1M7ha/?spm_id_from=333.337.search-card.all.click&vd_source=7b5ac179a9f675fb392df411e4abcf19)
	https://github.com/tporadowski/redis
	https://github.com/lework/RedisDesktopManager-Windows
2. 测试：auth 123456<mark style="background: #FFF3A3A6;"> 输入密码</mark>
3. 服务名：redis-windows
4. ![[Pasted image 20250428170722.png]]
5. keys pattern 大数据量的模糊查询会给服务器带来很大负担，不建议生产环境使用
	del key 删除key
	mset key1 value1 key2 value2 ...批量插入
	exists key 是否存在
	expire key seconds 设置Key有效期
	ttl key 查看key的剩余时长
6. <mark style="background: #FFF3A3A6;">shutdown之后，使用redis-server开启服务，然后就可以连接服务了（redis-server窗口不关闭）</mark>