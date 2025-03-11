

### **Callable 接口的作用**
**一句话总结**：Callable 是比 Runnable 更高级的“任务”，它能让线程**返回结果**，还能**抛出异常**。

#### **对比 Runnable**
- **Runnable**：`run()` 方法，无返回值，不能抛受检异常
```java
Runnable r = () -> { /* 干完活啥也不返回 */ };
```
- **Callable**：`call()` 方法，有返回值（泛型指定），可抛异常
```java
Callable<String> c = () -> { return "结果"; };
```

---

### **main 方法逐行解释**
#### **代码回顾**
```java
public static void main(String[] args) throws Exception {
    ExecutorService executor = Executors.newSingleThreadExecutor(); // 1. 创建线程池
    Future<String> future = executor.submit(new MyCallable());      // 2. 提交任务
    System.out.println(future.get());                               // 3. 获取结果
    executor.shutdown();                                            // 4. 关闭线程池
}
```

---

#### **1. `ExecutorService executor = Executors.newSingleThreadExecutor();`**
- **作用**：创建一个只有1个线程的线程池
- **类比**：雇了一个工人（线程），专门帮你做任务
- **知识点**：
  - `Executors`：工具类，快速创建线程池（如固定大小、缓存等）
  - `newSingleThreadExecutor`：单线程池（保证任务顺序执行）

---

#### **2. `Future<String> future = executor.submit(new MyCallable());`**
- **作用**：将 `MyCallable` 任务提交给线程池，返回一个“未来凭证”（`Future`）
- **类比**：把任务交给工人，他给你一张发票（Future），凭票取结果
- **知识点**：
  - `submit()`：提交任务，支持 `Callable` 或 `Runnable`
  - `Future`：异步计算结果（可查询状态、获取结果、取消任务）

---

#### **3. `System.out.println(future.get());`**
- **作用**：阻塞当前线程（主线程），直到任务完成并获取返回值
- **细节**：
  - 如果任务未完成，`get()` 会一直等待
  - 可以设置超时时间：`future.get(5, TimeUnit.SECONDS)`
- **风险**：如果任务卡死，主线程也会一直卡住！

---

#### **4. `executor.shutdown();`**
- **作用**：关闭线程池（不再接受新任务，已提交的任务继续执行完）
- **注意**：如果忘记关闭，线程池会一直驻留，导致程序无法终止！
- **其他关闭方式**：
  - `shutdownNow()`：立即停止所有任务（包括正在运行的）

---

### **涉及知识点总结**
| **知识点**               | **说明**                                                                 |
|--------------------------|-------------------------------------------------------------------------|
| **Callable 接口**         | 定义带返回值的线程任务，可抛出异常                                         |
| **Executor 框架**         | 线程池管理工具（避免手动创建线程）                                         |
| **Future 对象**           | 代表异步计算结果，提供结果获取、任务取消等功能                              |
| **线程池关闭**            | 正确关闭线程池避免资源泄漏                                                |
| **submit() vs execute()**| `submit()` 可接收 Callable/Runnable，返回 Future；`execute()` 只能接收 Runnable | 

---

### **代码执行流程**
```
主线程
  │
  ├─> 创建单线程池（工人就位）
  │
  ├─> 提交任务（给工人派活）
  │
  ├─> 主线程卡在 future.get()（等工人干完活拿结果）
  │
  └─> 拿到结果后关闭线程池（下班）
```