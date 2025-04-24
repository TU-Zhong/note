这是一个很好的问题。让我解释ThreadLocal和BaseContext的工作原理：

1. 首先，在拦截器中，当JWT token验证通过后，会将员工ID存入ThreadLocal：

```java
JwtTokenAdminInterceptor.java

sky-server/src/main/java/com/sky/interceptor

Claims claims = JwtUtil.parseJWT(jwtProperties.getAdminSecretKey(), token);

Long empId = Long.valueOf(claims.get(JwtClaimsConstant.EMP_ID).toString());

BaseContext.setCurrentId(empId);  // 将员工ID存入ThreadLocal
```


2. BaseContext的实现：
```java
BaseContext.java

sky-common/src/main/java/com/sky/context

Copy

public class BaseContext {

    public static ThreadLocal<Long> threadLocal = new ThreadLocal<>();

    public static void setCurrentId(Long id) {

        threadLocal.set(id);

    }

    public static Long getCurrentId() {

        return threadLocal.get();

    }

}
```

工作流程：

1. 当HTTP请求进来时，首先经过`JwtTokenAdminInterceptor`拦截器
2. 拦截器验证token后，调用`BaseContext.setCurrentId(empId)`存储员工ID
3. 由于ThreadLocal的特性，这个ID与当前线程绑定
4. 在同一个线程中的后续操作（如`EmployeeServiceImpl.save()`方法）可以通过`BaseContext.getCurrentId()`获取到这个ID

关键点：

- ThreadLocal为每个线程提供独立的变量副本
- 同一个请求在整个处理过程中都在同一个线程中执行
- 不需要显式传递，就能在同一线程的任何位置访问到存储的值

示意图：

Copy

HTTP请求 → JwtTokenAdminInterceptor(存储ID) → Controller → Service(获取ID) → 结束请求

	                  ↓                                               ↑

                  ThreadLocal  <----------------------------- 同一线程访问

这就是为什么在

`EmployeeServiceImpl`

中可以直接使用`BaseContext.getCurrentId()`获取当前登录用户的ID，因为这些代码都在处理同一个请求的同一个线程中执行。

需要注意：

- ThreadLocal的值只在当前线程有效
- 每个请求都会有自己的ThreadLocal值
- 请求结束后，ThreadLocal中的值应该被清除（通常在拦截器的afterCompletion方法中）