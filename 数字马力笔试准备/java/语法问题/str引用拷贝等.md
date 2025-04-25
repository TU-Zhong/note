```java
以下程序的输出结果是？
public class Example {
    String str = new String("good");
    char[] ch = { 'a', 'b', 'c' };
 
    public static void main(String args[]) {
        Example ex = new Example();
        ex.change(ex.str, ex.ch);
        System.out.print(ex.str + " and ");
        System.out.print(ex.ch);
    }
 
   public static void change(String str, char ch[])      
   {
        str = "test ok";
        ch[0] = 'g';
    }
}
用通俗的话给我解释
```


程序的输出结果是：**good and gbc**  

---

### **通俗解释**  
1. **关于 `str`（字符串）**  
   - 在 `ex` 对象中，`str` 初始值是 `"good"`。  
   - 调用 `change(ex.str, ex.ch)` 时，将 `ex.str` 的**引用拷贝**传给方法内的局部变量 `str`。  
   - 方法内 `str = "test ok";` 只是让**局部变量** `str` 指向了一个新的字符串 `"test ok"`，原对象的 `ex.str` **未被修改**。  

   **图解**：  
   ```
   ex.str --> "good" （未被改动）
   方法内的 str --> 先指向 "good"，后改成指向 "test ok"
   ```  

2. **关于 `ch`（字符数组）**  
   - `ex.ch` 初始是 `{'a', 'b', 'c'}`。  
   - 调用方法时，传递的是数组的**引用拷贝**，但方法内的 `ch` 和原 `ex.ch` 指向**同一个数组**。  
   - 方法内 `ch[0] = 'g';` 直接修改了原数组的内容。  

   **图解**：  
   ```
   ex.ch --> ['a', 'b', 'c'] → 方法内修改 → ['g', 'b', 'c']
   ```  

---

### **关键点总结**  
- **字符串不可变**：Java 中 `String` 是不可变对象，任何修改都会创建新对象，不影响原引用。  
- **数组是引用传递**：传递的是引用的拷贝，但修改内容会影响原数组。  
- **值传递的本质**：Java 中所有参数传递都是“值传递”（拷贝值），只是对象和数组的值是内存地址的拷贝。  

这样处理后，最终输出 `ex.str` 还是原来的 `"good"`，而 `ex.ch` 变成了 `gbc`。