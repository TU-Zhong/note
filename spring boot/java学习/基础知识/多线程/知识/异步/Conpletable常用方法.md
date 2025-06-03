好的，`CompletableFuture` (自 Java 8 引入) 是 Java 中进行异步编程的强大工具，它提供了非常丰富的方法来创建、编排和管理异步任务。下面我将罗列并介绍一些它常用的方法，并按功能分类：

---

### 一、创建 `CompletableFuture` 对象

这些方法用于启动异步任务并获取一个 `CompletableFuture` 实例。

1. **`runAsync(Runnable runnable)`**:
    
    - 执行一个没有返回值的异步任务 (`Runnable`)。
    - 默认使用 `ForkJoinPool.commonPool()` 作为线程池。
    - 返回 `CompletableFuture<Void>`。
    - 示例：`CompletableFuture<Void> future = CompletableFuture.runAsync(() -> System.out.println("异步任务执行..."));`
2. **`runAsync(Runnable runnable, Executor executor)`**:
    
    - 与上一个类似，但允许你指定自定义的线程池 (`Executor`) 来执行任务。
3. **`supplyAsync(Supplier<U> supplier)`**:
    
    - 执行一个有返回值的异步任务 (`Supplier<U>`)。
    - 默认使用 `ForkJoinPool.commonPool()`。
    - 返回 `CompletableFuture<U>`，其中 `U` 是 `supplier` 的返回类型。
    - 示例：`CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> "异步任务结果");`
4. **`supplyAsync(Supplier<U> supplier, Executor executor)`**:
    
    - 与上一个类似，但允许你指定自定义的线程池。
5. **`completedFuture(U value)`**:
    
    - 创建一个已经完成的 `CompletableFuture`，并指定其结果值。
    - 这对于将一个已知的值包装成 `CompletableFuture` 以便加入到异步链中非常有用。
    - 示例：`CompletableFuture<String> future = CompletableFuture.completedFuture("已预设的结果");`

---

### 二、结果处理与转换 (Completion Stage Methods - 同步执行)

当 `CompletableFuture` 完成时，这些方法用于处理或转换其结果。默认情况下，这些回调方法可能在完成前一个阶段的线程中执行，或者在调用这些方法的线程中执行（如果 Future 已经完成）。

1. **`thenApply(Function<? super T,? extends U> fn)`**:
    
    - 当 `CompletableFuture<T>` 正常完成时，将其结果 `T` 作为输入传递给 `Function fn`，该函数返回类型为 `U` 的结果。
    - 返回一个新的 `CompletableFuture<U>`。
    - 如果原始 Future 异常完成，则此阶段也会异常完成。
    - 示例：`future.thenApply(result -> result.toUpperCase());`
2. **`thenAccept(Consumer<? super T> action)`**:
    
    - 当 `CompletableFuture<T>` 正常完成时，将其结果 `T` 作为输入传递给 `Consumer action` 进行消费（无返回值）。
    - 返回 `CompletableFuture<Void>`。
    - 示例：`future.thenAccept(result -> System.out.println("结果是: " + result));`
3. **`thenRun(Runnable action)`**:
    
    - 当 `CompletableFuture<T>` 正常完成时，执行 `Runnable action`。这个动作不依赖于前一个 Future 的结果。
    - 返回 `CompletableFuture<Void>`。
    - 示例：`future.thenRun(() -> System.out.println("任务完成后的操作"));`

---

### 三、结果处理与转换 (Completion Stage Methods - 异步执行)

这些方法与上一组类似，但它们提供 `Async` 版本，允许你指定回调函数在哪个线程池中执行。

1. **`thenApplyAsync(Function<? super T,? extends U> fn)`**:
    - 类似 `thenApply`，但 `fn` 会被提交到 `ForkJoinPool.commonPool()` 中异步执行。
2. **`thenApplyAsync(Function<? super T,? extends U> fn, Executor executor)`**:
    - 类似 `thenApply`，但 `fn` 会被提交到指定的 `executor` 中异步执行。
3. **`thenAcceptAsync(Consumer<? super T> action)` / `thenAcceptAsync(Consumer<? super T> action, Executor executor)`**:
    - 类似 `thenAccept` 的异步版本。
4. **`thenRunAsync(Runnable action)` / `thenRunAsync(Runnable action, Executor executor)`**:
    - 类似 `thenRun` 的异步版本。

---

### 四、链式异步操作 (Chaining Dependent Asynchronous Operations)

当一个异步操作依赖于另一个异步操作的结果时使用。

1. **`thenCompose(Function<? super T, ? extends CompletionStage<U>> fn)`**:
    - 非常重要的方法，用于将两个**依赖的**异步操作串联起来。
    - 当 `CompletableFuture<T>` 完成时，其结果 `T` 会作为输入传递给 `Function fn`。这个 `fn` 函数本身必须返回一个 `CompletionStage<U>` (通常是另一个 `CompletableFuture<U>`)。
    - `thenCompose` 会返回一个新的 `CompletableFuture<U>`，它代表了 `fn` 返回的那个内部 `CompletableFuture` 的最终结果。
    - 这避免了 `CompletableFuture<CompletableFuture<U>>` 这样的嵌套结构。
    - 示例：
        
        Java
        
        ```
        CompletableFuture<String> userIdFuture = CompletableFuture.supplyAsync(() -> getUserId());
        CompletableFuture<String> userDetailsFuture = userIdFuture.thenCompose(userId ->
            CompletableFuture.supplyAsync(() -> getUserDetails(userId))
        );
        ```
        
2. **`thenComposeAsync(...)`**: `thenCompose` 的异步版本。

---

### 五、组合多个 `CompletableFuture`

用于处理多个独立的 `CompletableFuture`。

1. **`thenCombine(CompletionStage<? extends U> other, BiFunction<? super T,? super U,? extends V> fn)`**:
    - 当**当前 `CompletableFuture<T>`** 和 **另一个 `other<U>`** 都正常完成时，将它们的结果 `T` 和 `U` 作为参数传递给 `BiFunction fn`，该函数返回一个 `V` 类型的结果。
    - 返回一个新的 `CompletableFuture<V>`。
    - 示例：
        
        Java
        
        ```
        CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");
        CompletableFuture<String> combinedFuture = future1.thenCombine(future2, (s1, s2) -> s1 + " " + s2);
        // combinedFuture 的结果将是 "Hello World"
        ```
        
2. **`thenAcceptBoth(CompletionStage<? extends U> other, BiConsumer<? super T, ? super U> action)`**:
    - 类似 `thenCombine`，但使用 `BiConsumer` 来消费两个 Future 的结果，不产生新的结果值。返回 `CompletableFuture<Void>`。
3. **`runAfterBoth(CompletionStage<?> other, Runnable action)`**:
    - 当两个 Future 都完成后，执行一个 `Runnable` 动作，不关心它们的结果。返回 `CompletableFuture<Void>`。
4. **`allOf(CompletableFuture<?>... cfs)`**:
    - **静态方法**。接收一个或多个 `CompletableFuture` 作为参数。
    - 返回一个新的 `CompletableFuture<Void>`，这个新的 Future 会在**所有**传入的 `CompletableFuture` 都完成时才完成。
    - 如果任何一个传入的 Future 异常完成，则 `allOf` 返回的 Future 也会异常完成（使用第一个发生的异常）。
    - 注意：`allOf` 本身的返回类型是 `CompletableFuture<Void>`。如果你需要获取所有 Future 的结果，通常需要进一步处理，例如：
        
        Java
        
        ```
        CompletableFuture<Void> allDone = CompletableFuture.allOf(future1, future2, future3);
        allDone.thenRun(() -> {
            // 此时 future1, future2, future3 都已完成
            String result1 = future1.join(); // 使用 join() 或 get() 获取结果
            // ...
        });
        ```
        
5. **`anyOf(CompletableFuture<?>... cfs)`**:
    - **静态方法**。接收一个或多个 `CompletableFuture` 作为参数。
    - 返回一个新的 `CompletableFuture<Object>`，这个新的 Future 会在**任何一个**传入的 `CompletableFuture` 完成时就完成，其结果是第一个完成的 Future 的结果。
    - 如果第一个完成的 Future 是异常完成，则 `anyOf` 返回的 Future 也会异常完成。

---

### 六、异常处理

1. **`exceptionally(Function<Throwable, ? extends T> fn)`**:
    
    - 如果 `CompletableFuture<T>` 在执行过程中抛出异常，这个方法会捕获该异常，并将 `Throwable` 对象作为参数传递给 `Function fn`。
    - `fn` 函数可以处理这个异常，并返回一个 `T` 类型的替代结果，从而使整个异步链能够从异常中恢复并继续。
    - 返回一个新的 `CompletableFuture<T>`。
    - 示例：`future.exceptionally(ex -> "默认值或错误信息: " + ex.getMessage());`
2. **`whenComplete(BiConsumer<? super T, ? super Throwable> action)`**:
    
    - 当 `CompletableFuture<T>` 完成时（无论是正常完成还是异常完成），都会执行 `BiConsumer action`。
    - `action` 接收两个参数：结果 `T` (如果正常完成则非 null) 和异常 `Throwable` (如果异常完成则非 null)。
    - **这个方法不会改变原始 `CompletableFuture` 的结果或异常状态。**它主要用于记录日志、资源清理等副作用操作。
    - 返回一个新的 `CompletableFuture<T>`，其结果与原始 Future 相同。
    - 示例：`future.whenComplete((result, ex) -> { if (ex != null) System.err.println("出错了: " + ex.getMessage()); });`
3. **`handle(BiFunction<? super T, Throwable, ? extends U> fn)`**:
    
    - 类似于 `whenComplete`，但它更强大，因为 `BiFunction fn` 可以返回一个新的结果 `U`。
    - `fn` 接收结果 `T` 和异常 `Throwable`。它可以根据这两个参数决定返回什么，从而可以同时处理正常路径和异常路径，并可能转换结果类型。
    - 返回一个新的 `CompletableFuture<U>`。
    - 示例：`future.handle((result, ex) -> { if (ex != null) return "错误: " + ex.getMessage(); return "成功: " + result; });`

---

### 七、手动完成 `CompletableFuture`

这些方法允许你在代码的其他部分显式地完成一个 `CompletableFuture`。

1. **`complete(T value)`**:
    - 如果 `CompletableFuture` 尚未完成，则将其标记为正常完成，并设置结果为 `value`。
    - 如果已经完成，则此方法无效。
    - 返回 `true` 如果此次调用导致 Future 完成，否则返回 `false`。
2. **`completeExceptionally(Throwable ex)`**:
    - 如果 `CompletableFuture` 尚未完成，则将其标记为异常完成，并设置异常为 `ex`。
    - 如果已经完成，则此方法无效。
    - 返回 `true` 如果此次调用导致 Future 完成，否则返回 `false`。
3. **`obtrudeValue(T value)` / `obtrudeException(Throwable ex)`**:
    - 强制设置 Future 的结果/异常，即使它已经完成了。**应谨慎使用**，因为它会覆盖之前的完成状态。

---

### 八、其他常用方法 (继承自 `Future` 或自身提供)

1. **`get()`**: 阻塞当前线程，直到 `CompletableFuture` 完成并返回结果。如果 Future 异常完成，则抛出相应的 `ExecutionException`。
2. **`get(long timeout, TimeUnit unit)`**: 带超时的 `get()`。如果在指定时间内未完成，则抛出 `TimeoutException`。
3. **`join()`**: 类似于 `get()`，但它在 Future 异常完成时抛出的是未经检查的 `CompletionException` (包装了原始异常)。这在 Lambda 表达式中可能更方便，因为不需要显式处理受检查异常。
4. **`getNow(T valueIfAbsent)`**: 如果 `CompletableFuture` 已经完成，则立即返回其结果；否则返回给定的 `valueIfAbsent`，不会阻塞。
5. **`isDone()`**: 判断 `CompletableFuture` 是否已经完成（正常、异常或取消）。
6. **`isCompletedExceptionally()`**: 判断是否因为异常而完成。
7. **`isCancelled()`**: 判断是否被取消。
8. **`cancel(boolean mayInterruptIfRunning)`**: 尝试取消 `CompletableFuture` 的执行。

---

**使用 `Async` 后缀的注意事项：**

- 带有 `Async` 后缀的方法（如 `thenApplyAsync`, `thenRunAsync`）通常会将后续任务提交到线程池中执行，从而实现真正的异步化。
- 如果不带 `Async` 后缀，回调函数的执行线程取决于前一个阶段完成时的线程：
    - 如果前一个阶段已经完成，回调可能在调用该方法的当前线程中立即执行。
    - 如果前一个阶段尚未完成，回调通常会在完成前一个阶段的那个线程中执行。
- 如果你需要确保回调在不同的线程中执行以避免阻塞关键线程，或者需要指定特定的线程池，应优先使用 `Async` 版本。

`CompletableFuture` 的方法非常多，但理解了上述这些常用的方法及其模式，就能很好地应对大多数异步编程场景了。核心在于理解其**链式调用、非阻塞回调和组合能力**。