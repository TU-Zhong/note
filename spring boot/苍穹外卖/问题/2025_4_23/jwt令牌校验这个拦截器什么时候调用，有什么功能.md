让我详细解释

`JwtTokenAdminInterceptor.java`

这个拦截器：
```java
JwtTokenAdminInterceptor.java

sky-server/src/main/java/com/sky/interceptor


@Component

@Slf4j

public class JwtTokenAdminInterceptor implements HandlerInterceptor {

    @Autowired

    private JwtProperties jwtProperties;

    public boolean preHandle(HttpServletRequest request, 

    HttpServletResponse response, Object handler) throws Exception {

        //判断当前拦截到的是Controller的方法还是其他资源

        if (!(handler instanceof HandlerMethod)) {

            //当前拦截到的不是动态方法，直接放行

            return true;

        }

        //1、从请求头中获取令牌

        String token = request.getHeader(jwtProperties.getAdminTokenName

        ());

        //2、校验令牌

        try {

            log.info("jwt校验:{}", token);

            Claims claims = JwtUtil.parseJWT(jwtProperties.

            getAdminSecretKey(), token);

            Long empId = Long.valueOf(claims.get(JwtClaimsConstant.

            EMP_ID).toString());

            log.info("当前员工id：", empId);

            //3、通过，放行

            return true;

        } catch (Exception ex) {

            //4、不通过，响应401状态码

            response.setStatus(401);

            return false;

        }

    }

}
```


执行时机：

1. 这个拦截器会在所有匹配的HTTP请求到达Controller之前执行
2. 根据配置文件 
    
    `WebMvcConfiguration.java`
    
    ，它只拦截 `/admin/**` 路径的请求：


```java
WebMvcConfiguration.java

sky-server/src/main/java/com/sky/config



protected void addInterceptors(InterceptorRegistry registry) {

    log.info("开始注册自定义拦截器...");

    registry.addInterceptor(jwtTokenAdminInterceptor)

            .addPathPatterns("/admin/**")

            .excludePathPatterns("/admin/employee/login");

}
```

主要功能：

1. JWT令牌验证：
    - 从请求头中获取token（token名称在配置文件中定义）
    - 使用密钥验证token的有效性
    - 从token中提取员工ID信息
2. 请求过滤：
    - 对于非Controller方法的请求（如静态资源），直接放行
    - 对于token验证失败的请求，返回401状态码
    - 对于token验证通过的请求，允许继续访问Controller
3. 安全控制：
    - 确保只有携带有效token的请求才能访问管理端API
    - 登录接口 `/admin/employee/login` 被排除在拦截器之外
    - 防止未授权访问管理端接口

使用场景示例：

1. 当用户登录成功后，服务器生成JWT token返回给前端
2. 前端在后续请求中将token放在请求头中
3. 这个拦截器会验证每个管理端请求的token
4. 如果token无效或过期，请求将被拒绝（返回401）

这是一个典型的基于JWT的认证机制实现，用于保护管理端API的安全性。