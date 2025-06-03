

用“厨师做饭”的比喻，彻底搞懂中断机制和锁的关系！ 👨🍳

---

### **直接答案**
**如果线程已经拿到了锁**，调用 `thread.interrupt()` **不会强制中断线程，也不会释放锁**！线程会继续执行，直到**主动检查中断状态**并决定退出，或者**执行到可中断方法**（如 `sleep()`、`wait()`）时抛出 `InterruptedException`。

---

### **分场景解释**

#### **场景1：线程拿到锁，正在运行中**
```java
ReentrantLock lock = new ReentrantLock();

Thread thread = new Thread(() -> {
    lock.lock();
    try {
        System.out.println("线程拿到锁，开始做饭...");
        while (!Thread.currentThread().isInterrupted()) { 
            // 持续做饭（不检查中断状态）
        }
        System.out.println("检测到中断，停止做饭！");
    } finally {
        lock.unlock();
    }
});

thread.start();
Thread.sleep(1000);
thread.interrupt(); // 中断线程
```
**结果**：  
线程**不会停止做饭**！因为它在同步块内未检查中断状态（`isInterrupted()`），会一直运行。

---

#### **场景2：线程拿到锁后，执行可中断方法**
```java
lock.lock();
try {
    System.out.println("线程拿到锁，开始做饭...");
    Thread.sleep(5000); // 可中断方法
} catch (InterruptedException e) {
    System.out.println("做饭被中断！");
} finally {
    lock.unlock();
}
```
**调用 `interrupt()` 后**：  
线程在 `sleep()` 处抛出 `InterruptedException`，**但锁已经被释放**（因为 `unlock()` 在 `finally` 中）。

---

#### **场景3：线程主动检查中断状态**
```java
lock.lock();
try {
    System.out.println("线程拿到锁，开始做饭...");
    while (true) {
        if (Thread.currentThread().isInterrupted()) {
            System.out.println("检测到中断，停止做饭！");
            break;
        }
        // 做饭逻辑...
    }
} finally {
    lock.unlock();
}
```
**调用 `interrupt()` 后**：  
线程检测到中断状态，主动退出循环并释放锁。

---

### **关键结论**
| **情况**                | **中断效果**                                                                 |
|-------------------------|-----------------------------------------------------------------------------|
| 线程未持有锁，等待锁中   | 用 `lockInterruptibly()` 可中断等待；用 `lock()` 不可中断                   |
| 线程已持有锁，运行中     | `interrupt()` 仅设置中断标志，线程需主动检查才能响应                        |
| 线程持有锁并执行阻塞操作 | 若阻塞操作可中断（如 `sleep()`），会抛出异常并释放锁（需 `finally` 释放锁） |

---

### **最佳实践**
1. **在长时间任务中定期检查中断状态**：
   ```java
   while (!Thread.currentThread().isInterrupted()) {
       // 执行任务
   }
   ```
2. **在 `finally` 块中释放锁**：确保锁被释放，避免死锁
3. **优先使用可中断的锁获取方式**（如 `lockInterruptibly()`）处理长时间等待

---

### **总结口诀**  
中断不抢锁，运行继续干；  
检查靠自己，阻塞可截断！ 🔒🚨