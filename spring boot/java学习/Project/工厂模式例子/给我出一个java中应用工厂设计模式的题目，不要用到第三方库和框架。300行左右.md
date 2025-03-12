

**Java 设计模式练习题 - 工厂模式实现支付系统**

**题目描述**  
实现一个简单的支付处理系统，允许用户通过不同支付方式（信用卡、PayPal、加密货币）完成支付。要求：  
1. 使用**工厂设计模式**实现支付处理器对象的创建  
2. 所有支付处理器实现统一的接口  
3. 支持动态扩展新的支付方式  
4. 代码总量约300行（包含注释）

---

**具体要求**  
1. **接口定义**  
   创建 `PaymentProcessor` 接口包含以下方法：  
   - `boolean validate()`：验证支付信息  
   - `String processPayment(double amount)`：处理支付  
   - `String getPaymentType()`：获取支付类型

2. **具体实现类**  
   实现三个支付方式（每个类约40行）：  
   - **CreditCardProcessor**  
     - 属性：卡号、持卡人姓名、有效期、CVV  
     - 验证要求：有效期未过期、CVV为3位数字  
   - **PayPalProcessor**  
     - 属性：邮箱、密码  
     - 验证要求：邮箱格式正确、密码长度≥6  
   - **CryptoProcessor**  
     - 属性：钱包地址、私钥（模拟）  
     - 验证要求：钱包地址以"0x"开头且长度=42

3. **工厂实现**  
   - 创建 `PaymentProcessorFactory` 类  
   - 实现静态方法：  
     ```java
     public static PaymentProcessor createProcessor(String type, Map<String, String> params)
     ```  
   - 根据type参数("CREDIT_CARD", "PAYPAL", "CRYPTO")创建对应实例

4. **异常处理**  
   - 自定义 `PaymentException` 异常类  
   - 包含错误代码和错误信息

5. **演示程序**  
   - 创建 `PaymentDemo` 类演示以下流程：  
     ```
     用户选择支付方式 → 输入支付信息 → 验证 → 处理支付 → 显示结果
     ```  
   - 控制台交互示例：  
     ```
     可用支付方式：
     1. 信用卡
     2. PayPal
     3. 加密货币
     请输入选择：2
     请输入PayPal邮箱：user@example.com
     请输入密码：123456
     支付成功！[PAYPAL] 交易ID：PAYPAL-8d3b4a
     ```

---

**代码结构建议**  
```java
// 接口
interface PaymentProcessor {
    boolean validate() throws PaymentException;
    String processPayment(double amount);
    String getPaymentType();
}

// 异常类
class PaymentException extends Exception {
    private String errorCode;
    
    public PaymentException(String code, String message) {
        super(message);
        this.errorCode = code;
    }
    
    // 补充getter方法
}

// 工厂类
class PaymentProcessorFactory {
    // 实现createProcessor方法
}

// 具体处理器（示例）
class CreditCardProcessor implements PaymentProcessor {
    private String cardNumber;
    private String cardHolder;
    private String expiryDate;
    private String cvv;
    
    // 实现接口方法
    // 包含详细的参数校验逻辑
}

// 演示程序
public class PaymentDemo {
    private static final Scanner scanner = new Scanner(System.in);
    
    public static void main(String[] args) {
        // 实现交互逻辑
        // 使用工厂创建处理器
        // 异常处理等
    }
}
```

**考察重点**  
1. 工厂模式的正确定义  
2. 面向接口编程能力  
3. 参数验证的严谨性  
4. 异常处理完整性  
5. 代码的可扩展性设计  

**提示**  
- 可以使用`java.time.YearMonth`处理信用卡有效期  
- 参数验证推荐使用正则表达式  
- 交易ID可简单使用UUID随机部分  
- 保持控制台交互友好  

这个题目完整实现约需300行代码，能有效练习：  
- 工厂模式的核心实现  
- 面向对象设计原则  
- 输入验证与异常处理  
- 控制台交互开发