

在 Java 多线程编程中，临界区 (Critical Section) 是指一段代码，它访问共享资源（例如：变量、对象、文件等），并且在同一时间只允许一个线程执行这段代码。

简单来说，可以理解为：

- 共享资源 (Shared Resource): 多个线程都可能访问和修改的数据或对象。
    
- 临界区 (Critical Section): 操作这些共享资源的代码片段。
    
- 互斥 (Mutual Exclusion): 确保在任何时刻，只有一个线程能够进入并执行临界区内的代码。
    

为什么需要临界区？

如果多个线程同时访问和修改共享资源，而没有任何控制，就可能导致以下问题：

- 数据竞争 (Data Race): 当多个线程并发地访问同一块内存，并且至少有一个线程在写入时，可能会导致不可预测的结果。<mark style="background: #FFF3A3A6;">最终的值可能取决于线程执行的顺序</mark>，这使得程序的行为难以理解和调试。
    
- 不一致的状态 (Inconsistent State): <mark style="background: #FFF3A3A6;">如果一个线程在修改共享资源的过程中被中断，而此时另一个线程读取了这个资源，那么它可能会读到一个中间的、不完整或不正确的值。</mark>
    
- 死锁 (Deadlock): 两个或多个线程无限期地等待对方释放资源。
    

## Java 中如何处理临界区？

Java 提供了多种机制来保护临界区，确保线程安全：

1. synchronized 关键字:
    

- 同步方法 (Synchronized Methods): 在方法声明中使用 synchronized 关键字。当一个线程进入一个对象的同步方法时，它会获取该对象的锁。其他线程如果想进入该对象的任何同步方法，都必须等待锁被释放。  
    
```Java  
    public synchronized void criticalMethod() {  
        // 临界区代码  
        sharedResource++;  
    }  
      
    ```
- 同步块 (Synchronized Blocks): 使用 synchronized 关键字修饰一个代码块，并指定一个锁对象。只有获取了该锁对象的线程才能执行同步块中的代码。这提供了更细粒度的控制。  
    ``` java
    private Object lock = new Object();  
      
    public void anotherCriticalMethod() {  
        synchronized (lock) {  
            // 临界区代码  
            sharedResource--;  
        }  
    }  
    或者同步在 this 对象上：  
    public void yetAnotherCriticalMethod() {  
        synchronized (this) {  
            // 临界区代码  
            sharedResource++;  
        }  
    }  
      
       ```

2. java.util.concurrent.locks 包:
    

- Lock 接口及其实现类 (如 ReentrantLock): Lock 接口提供了比 synchronized 关键字更灵活和更强大的锁机制。例如，ReentrantLock 允许尝试获取锁（tryLock()）、可中断的锁获取以及公平性策略。  
    
```Java  
    import java.util.concurrent.locks.Lock;  
    import java.util.concurrent.locks.ReentrantLock;  
      
    private Lock lock = new ReentrantLock();  
    private int sharedResource = 0;  
      
    public void criticalOperation() {  
        lock.lock(); // 获取锁  
        try {  
            // 临界区代码  
            sharedResource++;  
        } finally {  
            lock.unlock(); // 在 finally 块中确保释放锁  
        }  
    }  
      
    ```

3. 原子变量 (Atomic Variables):
    

- java.util.concurrent.atomic 包提供了一些原子类，如 AtomicInteger, AtomicLong, AtomicBoolean 等。这些类对单个变量的操作提供了原子性的保证，通常比使用锁更高效，适用于简单的原子操作。  
    ```java

    import java.util.concurrent.atomic.AtomicInteger;  
      
    private AtomicInteger atomicSharedResource = new AtomicInteger(0);  
      
    public void atomicOperation() {  
        // 临界区代码 (原子操作)  
        atomicSharedResource.incrementAndGet();  
    }  
      ``` 
    

4. 其他并发工具:
    

- Semaphore: 允许指定数量的线程同时访问某个资源。
    
- CountDownLatch: 允许一个或多个线程等待其他线程完成操作。
    
- CyclicBarrier: 允许一组线程相互等待，直到所有线程都到达某个公共屏障点。
    

总结:

临界区是并发编程中一个非常核心的概念。正确地识别和保护临界区对于编写健壮、可靠的多线程 Java 应用程序至关重要。Java 提供的 synchronized 关键字和 java.util.concurrent 包中的工具类是实现这种保护的主要手段。选择哪种机制取决于具体的需和场景的复杂性。

Sources

1. [https://github.com/zhuhj083/myleetcode](https://github.com/zhuhj083/myleetcode)

