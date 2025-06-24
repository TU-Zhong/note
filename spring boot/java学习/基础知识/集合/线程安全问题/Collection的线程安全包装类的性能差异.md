好的，我们来看一个例子，这个例子会尝试展示 `Collections.synchronizedList` 在多线程高并发写操作下相对于某些 `java.util.concurrent` 包下的集合（这里用 `ConcurrentLinkedQueue` 作为对比，因为它非常适合高并发添加操作）可能表现出的性能差异。

**免责声明：**

- 这个例子是一个简化的基准测试，实际性能会受到很多因素影响（CPU核心数、JVM版本、具体操作类型、数据大小等）。
- 主要目的是演示 `synchronizedList` 的粗粒度锁在高并发写场景下可能成为瓶颈。
- `CopyOnWriteArrayList` 虽然是并发集合，但其写操作非常昂贵，在此场景下会比 `synchronizedList` 慢得多，所以不适合直接用来证明 `synchronizedList` 慢。我们用 `ConcurrentLinkedQueue` 来展示一个写性能优秀的并发集合。

**实验设计：**

1. 创建多个线程。
2. 每个线程向一个共享的集合中添加大量元素。
3. 我们比较以下三种情况（主要关注后两者）：
    - **`ArrayList` (无同步，多线程直接操作)**：预期会出错或数据不一致，用来说明为何需要同步。
    - **`Collections.synchronizedList(new ArrayList<>())`**：线程安全，但所有操作都通过一个锁同步。
    - **`ConcurrentLinkedQueue`**：专为高并发设计的队列，添加操作通常有更好的吞吐量。

Java
代码解释
[[spring boot/java学习/基础知识/集合/线程安全问题/代码解释/代码1]]
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Queue;
import java.util.concurrent.ConcurrentLinkedQueue;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

public class CollectionPerformanceDemo {

    // 测试参数
    private static final int NUM_THREADS = 10; // 线程数量
    private static final int OPERATIONS_PER_THREAD = 100_000; // 每个线程执行的操作次数 (添加元素)

    public static void main(String[] args) throws InterruptedException {
        System.out.println("警告：这是一个简化的性能演示，结果可能因环境而异。\n");

        // 1. ArrayList (无同步 - 预期会出错或数据不一致)
        // 注意：这个测试很可能因为线程不安全而出错，或者最终大小不正确
        // 通常不应该在多线程中这样直接使用 ArrayList
        List<Integer> arrayList = new ArrayList<>();
        System.out.println("开始测试: ArrayList (无同步，可能出错)...");
        try {
            performTest("ArrayList (Unsynchronized)", arrayList, (list, val) -> list.add(val));
        } catch (Exception e) {
            System.err.println("ArrayList (Unsynchronized) 测试中发生错误: " + e.getMessage());
            System.out.println("ArrayList (Unsynchronized) - 最终大小 (可能不准确): " + arrayList.size());
        }
        System.out.println("------------------------------------");


        // 2. Collections.synchronizedList
        List<Integer> synchronizedList = Collections.synchronizedList(new ArrayList<>());
        System.out.println("开始测试: Collections.synchronizedList...");
        performTest("Collections.synchronizedList", synchronizedList, (list, val) -> list.add(val));
        System.out.println("------------------------------------");

        // 3. ConcurrentLinkedQueue (作为高性能并发添加的对比)
        // 注意：ConcurrentLinkedQueue 不是 List，但它是一个高效的并发队列，适合此处的并发添加场景
        Queue<Integer> concurrentQueue = new ConcurrentLinkedQueue<>();
        System.out.println("开始测试: ConcurrentLinkedQueue...");
        performTestForQueue("ConcurrentLinkedQueue", concurrentQueue, (queue, val) -> queue.offer(val));
        System.out.println("------------------------------------");

    }

    // 通用测试方法 (List)
    private static <T extends List<Integer>> void performTest(String testName, T list, ListOperation<T> operation) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(NUM_THREADS);
        CountDownLatch latch = new CountDownLatch(NUM_THREADS); // 用于等待所有线程完成

        long startTime = System.nanoTime();

        for (int i = 0; i < NUM_THREADS; i++) {
            executor.submit(() -> {
                for (int j = 0; j < OPERATIONS_PER_THREAD; j++) {
                    operation.apply(list, j);
                }
                latch.countDown();
            });
        }

        latch.await(); // 等待所有任务完成
        long endTime = System.nanoTime();
        executor.shutdown();
        boolean terminated = executor.awaitTermination(1, TimeUnit.MINUTES); // 等待线程池关闭
        if (!terminated) {
            System.err.println(testName + " 线程池关闭超时!");
        }


        System.out.println(testName + " - 操作完成时间: " + (endTime - startTime) / 1_000_000 + " ms");
        System.out.println(testName + " - 预期的最终大小: " + (long)NUM_THREADS * OPERATIONS_PER_THREAD);
        System.out.println(testName + " - 实际的最终大小: " + list.size());
    }

    // 通用测试方法 (Queue)
    private static <T extends Queue<Integer>> void performTestForQueue(String testName, T queue, QueueOperation<T> operation) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(NUM_THREADS);
        CountDownLatch latch = new CountDownLatch(NUM_THREADS);

        long startTime = System.nanoTime();

        for (int i = 0; i < NUM_THREADS; i++) {
            executor.submit(() -> {
                for (int j = 0; j < OPERATIONS_PER_THREAD; j++) {
                    operation.apply(queue, j);
                }
                latch.countDown();
            });
        }

        latch.await();
        long endTime = System.nanoTime();
        executor.shutdown();
        boolean terminated = executor.awaitTermination(1, TimeUnit.MINUTES);
         if (!terminated) {
            System.err.println(testName + " 线程池关闭超时!");
        }

        System.out.println(testName + " - 操作完成时间: " + (endTime - startTime) / 1_000_000 + " ms");
        System.out.println(testName + " - 预期的最终大小: " + (long)NUM_THREADS * OPERATIONS_PER_THREAD);
        System.out.println(testName + " - 实际的最终大小: " + queue.size());
    }

    // 函数式接口用于操作
    @FunctionalInterface
    interface ListOperation<T extends List<Integer>> {
        void apply(T list, Integer value);
    }

    @FunctionalInterface
    interface QueueOperation<T extends Queue<Integer>> {
        void apply(T queue, Integer value);
    }
}
```

**预期结果与分析：**

1. **`ArrayList` (无同步)**:
    
    - **时间**：可能很快（因为它没有同步开销），也可能因为内部错误而提前终止。
    - **最终大小**：几乎肯定会小于 `NUM_THREADS * OPERATIONS_PER_THREAD`，因为会有写丢失（race condition）。甚至可能抛出 `ArrayIndexOutOfBoundsException` 或其他并发相关的异常。
    - **结论**：展示了为什么在多线程环境下直接使用非同步集合是危险的。
2. **`Collections.synchronizedList(new ArrayList<>())`**:
    
    - **时间**：会比无同步的 `ArrayList` 慢（因为有同步开销），并且这个时间会随着线程数的增加和竞争的加剧而显著增加。
    - **最终大小**：应该是正确的 (`NUM_THREADS * OPERATIONS_PER_THREAD`)，因为它是线程安全的。
    - **瓶颈**：所有的 `add` 操作都必须获取同一个锁。当多个线程尝试同时添加时，它们会相互等待，形成一个串行执行的瓶颈。只有一个线程能在任何给定时间修改列表。
3. **`ConcurrentLinkedQueue`**:
    
    - **时间**：通常会显著快于 `Collections.synchronizedList`。
    - **最终大小**：应该是正确的。
    - **原因**：`ConcurrentLinkedQueue` 使用了更细粒度的并发控制算法（例如 CAS - Compare-And-Swap 操作），允许多个线程在很大程度上并行地添加元素，而不需要获取一个全局锁。它的设计就是为了高并发的入队和出队操作。

**运行结果示例（在我的机器上，结果可能因你的环境而异）：**

```
警告：这是一个简化的性能演示，结果可能因环境而异。

开始测试: ArrayList (无同步，可能出错)...
ArrayList (Unsynchronized) 测试中发生错误: null // 或者其他并发错误，或者大小不对
ArrayList (Unsynchronized) - 最终大小 (可能不准确): 987654 // 示例：小于预期的 1000000
------------------------------------
开始测试: Collections.synchronizedList...
Collections.synchronizedList - 操作完成时间: 150 ms  // 示例时间
Collections.synchronizedList - 预期的最终大小: 1000000
Collections.synchronizedList - 实际的最终大小: 1000000
------------------------------------
开始测试: ConcurrentLinkedQueue...
ConcurrentLinkedQueue - 操作完成时间: 35 ms     // 示例时间，明显快于 synchronizedList
ConcurrentLinkedQueue - 预期的最终大小: 1000000
ConcurrentLinkedQueue - 实际的最终大小: 1000000
------------------------------------
```

**从这个简化的例子中，你可以“看到”：**

- `Collections.synchronizedList` 确实保证了线程安全，但其性能在高并发写操作下受到了全局锁的限制。
- 专门为并发设计的集合如 `ConcurrentLinkedQueue`（对于添加操作）可以提供好得多的吞吐量和性能，因为它们使用了更高级的并发技术来减少线程间的竞争和等待。

这就是为什么在需要高性能并发访问的场景下，通常推荐使用 `java.util.concurrent` 包下的集合，而不是简单的 `Collections.synchronizedXxx()` 包装器。包装器提供了一种简单的方式来为现有代码添加基本的线程安全性，但在高负载下可能成为性能瓶颈。