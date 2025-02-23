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

<mark style="background: #FFF3A3A6;">结论：恐怕之后的那些组件什么的要尽量写明版本，要不然就有可能出现这个下载了unknown的包的情况，导致组件（如lombok）无法生效。</mark>