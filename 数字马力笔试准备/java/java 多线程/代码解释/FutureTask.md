```java
FutureTask<String> futureTask=new FutureTask<>(new ThreadDemo5());  
Thread thread5=new Thread(futureTask);  
thread5.start();
```
这段代码展示了使用 `Callable` 和 `FutureTask` 创建线程的方式。让我详细解释：

`FutureTask` 是一个重要的并发工具类，它有以下特点：

1. 可以包装 `Callable` 任务，并且能够获取任务的执行结果
2. 实现了 `Runnable` 接口，因此可以被 `Thread` 执行
3. 提供了任务的状态查询、取消等功能

这段代码的执行过程是：

```java
// 1. 创建一个FutureTask，传入实现了Callable接口的ThreadDemo5实例
FutureTask<String> futureTask = new FutureTask<>(new ThreadDemo5());
// 2. 创建线程，将FutureTask作为任务传入
Thread thread5 = new Thread(futureTask);
// 3. 启动线程
thread5.start();
```
与 `Runnable` 相比，`Callable` 接口的优势在于：

1. 可以返回执行结果（通过 `FutureTask.get()` 获取）
2. 可以抛出异常
3. 支持泛型，可以指定返回值类型

例如，你可以这样获取执行结果：
```java
FutureTask<String> futureTask = new FutureTask<>(new ThreadDemo5());
Thread thread5 = new Thread(futureTask);
thread5.start();

try {
    // 获取线程执行结果，如果线程还没执行完，这里会阻塞等待
    String result = futureTask.get();
    System.out.println("线程执行结果：" + result); // 输出: 线程执行结果：Callable线程执行完成
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
}
```

在你的代码中，

`ThreadDemo5`

实现了 `Callable<String>` 接口，并在 `call()` 方法中返回了字符串 `"Callable线程执行完成"`。这个返回值就可以通过 `FutureTask.get()` 方法获取。