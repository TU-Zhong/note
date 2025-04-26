

（拍桌子）来来来！这可是面试必考题！咱们拆开揉碎说：

🔧 **基础必杀技**：
1. 生孩子大法（线程创建）：
   - 继承Thread（就像生儿子）→ `class MyThread extends Thread`
   - 实现Runnable（更像租个保姆干活）→ `new Thread(new MyRunnable())`
   - 带返回值的Callable（干完活给收据）→ `FutureTask`

2. 线程身份证（Thread API）：
   - start() vs run()（按启动键vs亲自动手）
   - sleep()（打盹不交权）
   - yield()（礼让三先）
   - join()（霸道插队）

3. 生命轮回（线程状态）：
   - NEW → 胚胎状态
   - RUNNABLE → 待命状态
   - BLOCKED → 卡在厕所门口
   - WAITING → 躺平等人叫
   - TIMED_WAITING → 设闹钟躺平
   - TERMINATED → 凉透了

🔒 **锁王争霸**：
1. 祖传锁头（synchronized）：
   - 代码块锁 → `synchronized(obj){...}`
   - 方法锁 → 直接怼方法上
   - 类锁 → `synchronized(MyClass.class)`

2. 高级防盗门（Lock体系）：
   - ReentrantLock（可重入锁）→ `lock.lock()/unlock()`
   - 读写锁（ReadWriteLock）→ 读读不互斥
   - Condition条件变量 → 精准叫醒服务

💣 **核弹级问题**：
1. 死锁四要素（必须全中）：
   - 互斥 → 独木桥只能过一人
   - 占有且等待 → 左手拿碗不放，右手还要抢筷子
   - 不可剥夺 → 抢到的东西死也不放
   - 循环等待 → A等B，B等C，C等A

2. 原子性问题：
   - i++其实是三步操作（读→改→写）
   - 用AtomicInteger救场 → `getAndIncrement()`

3. 内存屏障：
   - volatile关键字 → 禁止指令重排+强制刷新内存
   - happens-before原则 → 前事不忘后事之师

🚀 **实战王炸组合**：
1. 线程池七龙珠：
   - 核心参数（corePoolSize, maxPoolSize, queue）
   - 四大拒绝策略（AbortPolicy爆异常，CallerRunsPolicy让老板干活）
   - Executors工具类（小心OOM陷阱！）

2. JUC兵器库：
   - CountDownLatch（集齐七颗龙珠召唤神龙）
   - CyclicBarrier（旅行团等人齐出发）
   - Semaphore（公共厕所坑位管理）
   - ConcurrentHashMap（线程安全的菜篮子）

3. ThreadLocal玄学：
   - 线程专属储物柜
   - 内存泄漏警告！用完必须remove()

💡 **保命口诀**：
- 能不用锁尽量不用（减少竞争）
- 锁范围尽量小（快进快出）
- 永远不在同步块里调外部方法（避免死锁）
- 优先使用线程安全集合（CopyOnWriteArrayList之类）
- 异常要吃掉！线程抛异常会直接暴毙

（突然压低声音）偷偷告诉你：面试官最爱问volatile和synchronized区别，准备好800字小作文！