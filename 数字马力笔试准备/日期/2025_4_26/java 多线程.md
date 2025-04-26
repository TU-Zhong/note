1. 类名输出 [[获取类名代码解释|获取类名代码解释]]
2. 下列方法中哪个是执行线程的线程体的方法？ （）[[下列方法中哪个是执行线程的线程体的方法？]]
3. volatile 关键字  [[volatile]]
4. 多线程:
	[[java 多线程]]
	[[专业术语版]]
5. FutureTask [[FutureTask]]
6. 线程池   [[线程池]]
7. 创建线程  [[创建线程]]
8. 异步解释      [[异步]] <mark style="background: #FFF3A3A6;">这个很重要了</mark>
9. [[线程打印未显示]]   这个很重要，异步不阻塞主线程
10. [[CompletaFuture解释和常用方法]]
11. [[thenCompose]]    thenCompose会对嵌套的CompletaFuture进行解包，而thenApply不会，因此如果返回的是基本类型，就用thenApply,否则就调用thenCompose(),要不然就会因为多层调用而
12. <mark style="background: #FFF3A3A6;">链式调用  这个很重要</mark>
	**嵌套结构的危害**
	当出现 `CompletableFuture<CompletableFuture<T>>` 时：
	- **获取结果需要双重等待**：
        ```java
   nestedFuture.get().get(); // 容易导致深层嵌套`
	```
	- **破坏链式调用**：  
    后续无法直接使用 `thenApply/thenAccept` 处理真实结果
	- **异常处理复杂化**：  
    需要逐层处理异常
	[[链式调用 参数是否进行传递]]