

用银行柜台和总部的例子，秒懂 `synchronized` 方法锁的机制！🏦

---

### **一句话回答**  
`synchronized` 方法锁的是：  
- **实例方法** → 当前对象（`this`）  
- **静态方法** → **整个类**（`类名.class` 对象）

---

### **1. 实例方法（非静态方法）**
```java
public synchronized void 存钱() { 
    // 操作余额
}
```
**锁对象**：`this`（当前账户实例）  
**效果**：同一时间，**同一个账户实例**的其他 `synchronized` 实例方法会被阻塞（不同账户实例互不影响）

**比喻**：  
某个分行的窗口1（`this`）被锁，**同分行的其他窗口**（其他实例方法）要排队，但其他分行的窗口不受影响！

---

### **2. 静态方法**
```java
public static synchronized void 统计全行流水() { 
    // 操作全局统计量
}
```
**锁对象**：`Bank.class`（类的Class对象）  
**效果**：同一时间，**所有调用该静态方法的线程**（无论属于哪个实例）必须竞争同一把锁！

**比喻**：  
总部机房（`Bank.class`）的门锁了，所有分行的员工想进机房都要排队，不管你在哪个分行工作！

---

### **代码验证**
#### 场景1：同一实例的同步方法互斥
```java
class Account {
    public synchronized void methodA() { 
        System.out.println("methodA 开始");
        sleep(3000);
        System.out.println("methodA 结束");
    }
    
    public synchronized void methodB() {
        System.out.println("methodB 开始");
    }
}

// 测试代码：
Account acc = new Account();
new Thread(acc::methodA).start(); // 线程1
new Thread(acc::methodB).start(); // 线程2 → 必须等待线程1释放锁
```

**输出**：
```
methodA 开始
...3秒后...
methodA 结束
methodB 开始
```

---

#### 场景2：不同实例的同步方法互不干扰
```java
Account acc1 = new Account();
Account acc2 = new Account();

new Thread(acc1::methodA).start(); // 线程1 → 锁acc1
new Thread(acc2::methodA).start(); // 线程2 → 锁acc2 → 立即执行！
```

**输出**：
```
methodA 开始（线程1）
methodA 开始（线程2） ← 两个线程同时执行！
...3秒后...
methodA 结束（线程1）
methodA 结束（线程2）
```

---

#### 场景3：静态方法的类锁全局生效
```java
class Bank {
    public static synchronized void staticMethod() {
        System.out.println("静态方法开始");
        sleep(3000);
        System.out.println("静态方法结束");
    }
}

// 测试代码：
new Thread(Bank::staticMethod).start(); // 线程1
new Thread(Bank::staticMethod).start(); // 线程2 → 必须等待
```

**输出**：
```
静态方法开始（线程1）
...3秒后...
静态方法结束（线程1）
静态方法开始（线程2）
...3秒后...
静态方法结束（线程2）
```

---

### **关键结论表**
| **方法类型**       | **锁对象**          | **同步范围**              | **比喻**               |
|--------------------|--------------------|--------------------------|-----------------------|
| **实例同步方法**    | `this`（当前实例）  | 同一实例的所有实例同步方法 | 分行的某个窗口        |
| **静态同步方法**    | `类名.class`        | 全类所有实例的静态同步方法 | 总部的机房            |

---

### **最佳实践**
1. **避免滥用类锁**：除非保护静态变量，否则不要用静态同步方法（性能杀手）
2. **优先用代码块锁**：缩小锁范围，提高并发性能
   ```java
   // 更优写法：只锁需要保护的部分
   public void 存钱() {
       // 非同步代码
       synchronized(this) { // 锁范围更小
           // 操作余额
       }
   }
   ```
3. **区分实例锁与类锁**：二者互不干扰，但混用可能导致死锁

---

**口诀**：  
实例方法锁自己，静态方法锁全局；  
同步范围要搞清，避免错用成瓶颈！🔒