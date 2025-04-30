需要将

`User`

对象转换为`UserDTO`对象。可以使用 Hutool 工具包的 `BeanUtil.copyProperties` 方法来实现属性拷贝。修改代码如下：

```java

LoginInterceptor.java

src/main/java/com/hmdp/utils


        UserDTO userDTO = BeanUtil.copyProperties(user, 

        UserDTO.class);

        UserHolder.saveUser(userDTO);
```
需要在文件顶部添加 Hutool 的导入语句：
```java
import cn.hutool.core.bean.BeanUtil;
```
这样就可以将 session 中的`User`对象转换为`UserDTO`对象，并保存到`UserHolder`中。`BeanUtil.copyProperties` 会自动复制两个类中名称相同的属性。