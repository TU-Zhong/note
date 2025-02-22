```java
@Data  
public class aaa {  
    private String name;  
  
    public static void main(String[] args) {  
        aaa test = new aaa();  
        test.setName("success"); // 若能编译，说明问题出在原项目配置  
        System.out.println(test.getName());  
          
    }  
}
```
就这几句代码，一直无法正确运行，一直报错无法找到字符setName()。
Debug过程：
[[debug过程]]