这段代码绝对是你在准备后端开发面试，或者自己手写 Spring Boot 项目时，最拿得出手的亮点之一。

想象一下，在你的 Spring Boot 后端系统中（比如你正在为 Vue 前端写各种提供数据的接口），如果某个接口突然响应特别慢，你想找出到底是哪个底层方法耗时最长。如果没有 AOP，你得在每个方法里写一堆 `System.currentTimeMillis()` 相减的废话。

有了 AOP，我们只需要分三步，自己“造”一个轮子：

### 第一步：发明一个“标签”（自定义注解）

首先，我们利用 Java 的元注解，自己创造一个名为 `@LogExecutionTime` 的注解。这就相当于我们做了一张“安检贴纸”。

Java

```
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

// @Target 说明这个注解只能贴在方法上
@Target(ElementType.METHOD)
// @Retention 说明这个注解在程序运行期间一直存活（这样 AOP 才能扫描到它）
@Retention(RetentionPolicy.RUNTIME)
public @interface LogExecutionTime {
    // 这是一个空注解，单纯作为一个标记使用
}
```

### 第二步：制造“外骨骼装甲”（编写切面类）

这是最核心的一步！我们要写一个切面（Aspect），告诉 Spring：“只要看到谁的方法上贴了刚才那个贴纸，你就把它拦截下来，给它穿上这套测速装甲！”

这里我们使用最强大的 **`@Around`（环绕通知）**，因为它能彻底包裹住原方法。

Java

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect      // 告诉 Spring 这是一个切面（安保部门）
@Component   // 把这个切面交给 Spring 容器管理
public class LogAspect {

    // @Around 就是环绕通知。后面的表达式意思是：拦截所有标有 @LogExecutionTime 注解的方法！
    @Around("@annotation(LogExecutionTime)")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        
        // 1. 【方法执行前】：记录开始时间
        long start = System.currentTimeMillis();

        // 2. 【核心放行】：执行原本的业务方法（也就是没穿装甲前的那个方法）
        // proceed 接收了原方法的返回值，如果原方法抛异常，这里也会往外抛
        Object proceed = joinPoint.proceed(); 

        // 3. 【方法执行后】：计算总耗时
        long executionTime = System.currentTimeMillis() - start;

        // 获取原方法的名字，并打印日志
        String methodName = joinPoint.getSignature().getName();
        System.out.println("【性能监控】方法 [" + methodName + "] 执行耗时: " + executionTime + " ms");

        // 4. 把原方法的返回值老老实实地还回去，假装什么都没发生
        return proceed;
    }
}
```

### 第三步：给业务方法“穿装甲”（实战使用）

现在轮子造好了，用起来简直爽到飞起。你只需要在任何你想测速的方法上，轻轻贴上这个注解：

Java

```
import org.springframework.stereotype.Service;

@Service
public class DatabaseService {

    // 贴上我们自己发明的注解！
    @LogExecutionTime
    public void fetchMassiveData() throws InterruptedException {
        System.out.println(">>> 正在执行极其耗时的数据库全表扫描...");
        // 模拟执行耗时操作，睡 2 秒
        Thread.sleep(2000); 
    }
}
```

当你调用 `fetchMassiveData()` 方法时，控制台就会极其优雅地打印出：

Plaintext

```
>>> 正在执行极其耗时的数据库全表扫描...
【性能监控】方法 [fetchMassiveData] 执行耗时: 2005 ms
```

---

### 💡 面试官视角的加分项

在面试国企或者大厂时，如果你能把上面这段代码的大致逻辑说出来，不仅证明你懂 AOP 的概念，更证明你具备了**“抽象公共组件”**的架构思维。你可以自信地告诉面试官：

- **解耦**：这段测速代码完全没有侵入到业务逻辑中。
    
- **复用**：以后无论系统里新增多少个模块，只要想测速，直接贴注解就行，一行多余的代码都不用写。
    

其实，Spring 里最著名的 `@Transactional` 事务注解，底层用的就是一模一样的 `@Around` 环绕通知套路：在 `proceed()` 之前开启事务，在 `proceed()` 之后提交事务，如果捕获到异常就回滚事务。

既然聊到了这里，你想趁热打铁，看看 Spring 的 **`@Transactional` 事务在哪些阴间场景下会突然“失效”吗？**（这也是后端面试中频率极高的一个坑位）。