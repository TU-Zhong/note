1. 
```xml
<dependency>  
    <groupId>org.apache.commons</groupId>  
    <artifactId>commons-pool2</artifactId>  
    <version>2.12.0</version>  
</dependency>
引入池
<dependency>  
    <groupId>com.fasterxml.jackson.core</groupId>  
    <artifactId>jackson-databind</artifactId>  
    <version>2.18.3</version>  
</dependency>
```
引入依赖，否则会报错找不到类

2. lombok依赖不工作
	[[问题：lombok插件一直无法正确生成代码]]
3. 键名结构理解：
	user:100:profile
	可以理解为总文件夹user下的100文件夹下的profile文件夹,但其实也就是一个String字符串而已
4. 反序列化，序列化，存储类，并取出
	```java
	redisTemplate.opsForValue().set("user:100",new user("虎哥",21));  
	//存储的时候维护了类的字节码
	user o = (user) redisTemplate.opsForValue().get("user:100");
	```
	```java
	1//这里不将类信息传入redis表，而是只传入string字符串，然后读出手动转化成类
	private static final ObjectMapper mapper=new ObjectMapper();
	@Test  
	void restSavaUser2() throws JsonProcessingException {  
    user u=new user("虎哥",21);  
    //类转为字符串
    String json = mapper.writeValueAsString(u);  
    stringRedisTemplate.opsForValue().set("user:100",json);  
    String jsonUser=stringRedisTemplate.opsForValue().get("user:100");  
    //字符串转为类
    user u2 = mapper.readValue(jsonUser, user.class);  
    System.out.println("o="+u2);  
}
	```
5. 给我用傻子也能听懂的话解释stringRedisTemplate和RedisTemplate分别是什么，有什么不同
	[[给我用傻子也能听懂的话解释stringRedisTemplate和RedisTemplate分别是什么，有什么不同]]
	