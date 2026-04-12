为了让代码更清晰并且避免重复编写 `@annotation(LogExecutionTime)` 这个切入点表达式，**强烈建议先提取一个公共的 `@Pointcut`**。

在 `@Before` 和 `@After` 中，你需要使用 `JoinPoint` 来获取方法信息（注意：只有 `@Around` 才能使用 `ProceedingJoinPoint`，因为它需要控制方法的 `proceed()` 执行）。

下面是为你补充和优化后的完整代码：

Java

```
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;
import java.util.Arrays;

@Aspect
@Component
public class LogAspect {

    /**
     * 1. 定义公共切入点
     * 拦截所有标记了 @LogExecutionTime 注解的方法
     */
    @Pointcut("@annotation(LogExecutionTime)")
    public void logPointcut() {
    }

    /**
     * 2. Before 逻辑：在目标方法执行之前调用
     */
    @Before("logPointcut()")
    public void logBefore(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();
        // 如果需要，还可以获取入参：Object[] args = joinPoint.getArgs();
        System.out.println("[Before 通知] 事件记录：准备调用方法 [" + methodName + "]");
    }

    /**
     * 3. After 逻辑：在目标方法执行之后调用（无论成功还是抛出异常都会执行，类似 finally）
     */
    @After("logPointcut()")
    public void logAfter(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();
        System.out.println("[After 通知] 事件记录：方法 [" + methodName + "] 调用彻底结束");
    }

    /**
     * 4. 保留你原有的 Around 逻辑：包裹整个方法，计算耗时
     */
    @Around("logPointcut()")
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long start = System.currentTimeMillis();
        System.out.println("[Around 通知] 开始执行业务逻辑...");

        // 放行，执行目标方法
        Object proceed = joinPoint.proceed();

        long executionTime = System.currentTimeMillis() - start;
        String methodName = joinPoint.getSignature().getName();

        System.out.println("[Around 通知] 业务逻辑执行完毕，" + methodName + " 耗时: " + executionTime + "ms");
        
        return proceed;
    }
}
```

### 💡 核心细节说明：

1. **`@Pointcut` 的优势：** 将 `@annotation(LogExecutionTime)` 抽离成 `logPointcut()` 方法后，下面的 `@Before`、`@After` 和 `@Around` 都可以直接引用这个方法名。如果以后要修改拦截规则，只需改这一个地方。
    
2. **`JoinPoint` vs `ProceedingJoinPoint`：** * `@Before` 和 `@After` 的参数必须是 `JoinPoint`，它只能用来读取信息（比如方法名、参数 `getArgs()`）。
    
    - 只有 `@Around` 可以且必须使用 `ProceedingJoinPoint`，因为它继承了 `JoinPoint`，并且多了一个 `proceed()` 方法，让你有权力决定“是否执行目标方法”以及“何时执行目标方法”。
        
3. **执行顺序：** 当你同时使用这三个通知时，正常的执行顺序会是：
    
    `@Around 开始` -> `@Before` -> **目标方法执行** -> `@Around 结束` -> `@After`。