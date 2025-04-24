让我详细解释 ThreadLocal 和当前线程的绑定机制：

ThreadLocal 的核心原理是，它在每个线程内部都维护了一个 ThreadLocalMap 的数据结构。这个 Map 的 key 是 ThreadLocal 对象本身，value 是我们存储的值。

让我们通过源码来看：

1. 当我们调用 `BaseContext.setCurrentId()` 时：

```java
BaseContext.java

sky-common/src/main/java/com/sky/context

Copy

public class BaseContext {

    public static ThreadLocal<Long> threadLocal = new ThreadLocal<>();

    public static void setCurrentId(Long id) {

        threadLocal.set(id);  // 这里实际上调用了ThreadLocal.set()方法

    }

}
```

2. ThreadLocal 的 set() 方法内部实现（这是Java源码，不在你的项目中）：
```java
Copy

public void set(T value) {

    // 获取当前线程

    Thread t = Thread.currentThread();

    // 获取当前线程的ThreadLocalMap

    ThreadLocalMap map = getMap(t);

    if (map != null)

        // 将值存储到map中，this作为key（this指ThreadLocal对象本身）

        map.set(this, value);

    else

        // 如果map不存在，则创建map并存储值

        createMap(t, value);

}
```

3. 每个 Thread 对象都有一个 threadLocals 字段（这是Java源码，不在你的项目中）：

```java
public class Thread implements Runnable {

    ThreadLocal.ThreadLocalMap threadLocals = null;

    // ...

}
```



实际的存储结构如下：



Thread对象

  └── threadLocals (ThreadLocalMap类型)

       └── Entry数组

            ├── key: ThreadLocal对象

            └── value: 存储的值(在这里是员工ID)

举个例子：

Copy

// 假设有两个并发请求，分别由线程1和线程2处理

// 线程1处理用户A的请求

Thread-1: {

    threadLocals: {

        BaseContext.threadLocal -> 员工ID: 1001

    }

}

// 线程2处理用户B的请求

Thread-2: {

    threadLocals: {

        BaseContext.threadLocal -> 员工ID: 1002

    }

}

绑定过程：

1. 请求进入系统，由线程池中的某个线程处理
2. 在拦截器中调用 `BaseContext.setCurrentId(empId)`
3. ThreadLocal.set() 方法获取当前线程对象
4. 将值存储到该线程的 ThreadLocalMap 中
5. 后续在同一线程中的任何位置调用 `BaseContext.getCurrentId()`，都会从当前线程的 ThreadLocalMap 中获取值

示例代码流程：

Copy

// 在拦截器中

public boolean preHandle(...) {

    // 当前线程为 Thread-1

    Long empId = 1001;

    BaseContext.setCurrentId(empId);  // 存储到 Thread-1 的 ThreadLocalMap 中

    // ...

}

// 在 Service 中

public void save(...) {

    // 还是在 Thread-1 中执行

    Long currentId = BaseContext.getCurrentId();  // 从 Thread-1 的 ThreadLocalMap 中获取

    // currentId == 1001

}

这就是为什么每个线程都能访问到自己的值，而不会相互干扰的原理。这种机制特别适合在 Web 应用中传递用户上下文信息，因为每个请求都由独立的线程处理。

需要注意的是，使用完 ThreadLocal 后应该及时清理，避免内存泄漏：

Copy

@Override

public void afterCompletion(...) {

    BaseContext.removeCurrentId();  // 清理ThreadLocal中的数据

}