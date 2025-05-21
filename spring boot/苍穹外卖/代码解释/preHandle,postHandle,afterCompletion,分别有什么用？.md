好的，`preHandle`、`postHandle` 和 `afterCompletion` 是 Spring MVC 中 `HandlerInterceptor` 接口定义的三个核心方法。它们允许你在请求处理的不同阶段插入自定义逻辑，常用于实现日志记录、权限校验、性能监控、向模型添加公共数据等横切关注点。

下面分别解释它们的作用和执行时机：

### 1. `preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)`

- **作用与执行时机：**
    
    - 这是拦截器中最先被调用的方法。
    - 它在**目标处理器（Controller 方法）执行之前**被调用。
    - `handler` 参数通常是即将执行的处理器方法（`HandlerMethod` 实例），你可以通过它获取到方法名、注解等信息。
- **返回值：**
    
    - 返回一个**布尔值**。
    - **`true`**：表示请求继续向下执行，会依次调用后续的拦截器的 `preHandle` 方法，最后到达目标处理器。
    - **`false`**：表示请求被拦截，**不会继续向下执行**，后续的拦截器和目标处理器都不会被调用。此时，你可以通过 `response` 对象直接向客户端返回响应（例如，权限不足时返回401或403）。如果 `preHandle` 返回 `false`，则该拦截器的 `postHandle` 和 `afterCompletion` 方法都不会被执行，但之前已经执行过 `preHandle` 并返回 `true` 的拦截器的 `afterCompletion` 仍然会被调用。
- **常见用途：**
    
    - **权限校验/身份认证**：检查用户是否有权限访问该资源，如果没有则返回 `false` 并设置响应状态码。
    - **日志记录**：记录请求的入口信息，如请求IP、URL、参数等。
    - **请求参数预处理**：对请求参数进行一些修改或添加。
    - **CSRF（跨站请求伪造）防护**：校验CSRF令牌。
    - **请求限流**。

### 2. `postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)`

- **作用与执行时机：**
    
    - 在**目标处理器（Controller 方法）执行之后，但在DispatcherServlet进行视图渲染之前**被调用。
    - 也就是说，Controller 方法已经执行完毕，并且返回了 `ModelAndView` 对象（如果方法返回的是 `ModelAndView` 类型的话），此时你可以对这个 `ModelAndView` 进行修改。
    - **前提条件**：只有当对应的 `preHandle` 方法返回 `true` 并且处理器成功执行（没有抛出未处理的异常）时，`postHandle` 才会被调用。如果处理器执行过程中抛出异常，`postHandle` 不会被执行。
- **参数：**
    
    - `modelAndView`：处理器方法执行后返回的 `ModelAndView` 对象。你可以通过它修改模型数据（Model）或视图名称（View）。如果处理器方法没有返回 `ModelAndView` (例如，使用了 `@ResponseBody` 返回JSON数据，或者返回 `void`)，`modelAndView` 可能会是 `null` 或者是一个Spring内部创建的默认对象。
- **常见用途：**
    
    - **修改模型数据**：向 `ModelAndView` 中添加所有视图都需要的公共数据（例如，全局导航信息、当前用户信息）。
    - **修改视图名称**：根据某些条件动态改变将要渲染的视图。
    - **日志记录**：记录处理器执行的结果或耗时（虽然更精确的耗时可以在 `afterCompletion` 中记录）。
    - **响应内容处理**：在视图渲染前对响应内容做一些统一处理（但不常见，通常视图渲染本身会处理内容）。

### 3. `afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)`

- **作用与执行时机：**
    
    - 在**整个请求处理完成之后，即 DispatcherServlet 已经完成了视图渲染之后**被调用。
    - 这个方法的主要目的是进行资源清理工作。
    - **无论处理器方法是否成功执行，或者是否抛出异常，只要对应的 `preHandle` 方法返回 `true`，`afterCompletion` 方法就一定会被调用**。这使得它非常适合做一些保证最终会被执行的清理操作。
- **参数：**
    
    - `ex`：一个 `Exception` 对象。如果处理器执行过程中或视图渲染过程中抛出了异常，这个参数就会是该异常对象；如果没有异常，则为 `null`。
- **常见用途：**
    
    - **资源清理**：释放 `preHandle` 中获取的资源，例如关闭文件流、数据库连接（虽然现在通常由Spring管理）。
    - **异常处理/日志记录**：统一记录请求处理过程中发生的异常。
    - **性能监控**：记录整个请求处理（包括视图渲染）的总耗时。
    - **完成日志**：记录请求处理结束的标志。

**执行顺序总结（假设有多个拦截器 Interceptor1, Interceptor2）：**

1. Request -> Interceptor1.preHandle() (return true)
2. Request -> Interceptor2.preHandle() (return true)
3. Request -> Controller.handlerMethod()
4. (If no exception in Controller) -> Interceptor2.postHandle()
5. (If no exception in Controller) -> Interceptor1.postHandle()
6. Request -> View Rendering (if applicable)
7. Request -> Interceptor2.afterCompletion()
8. Request -> Interceptor1.afterCompletion()

注意 `postHandle` 和 `afterCompletion` 的执行顺序与 `preHandle` 是相反的（责任链模式的典型回溯过程）。

理解这三个方法的职责和调用时机，可以帮助你更灵活、更有效地使用Spring MVC的拦截器机制。