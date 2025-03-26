```java
package Calculator;  
  
import java.util.Stack;  
import java.util.regex.Pattern;  
  
public class check {  
    public int Check(String expr){  
        //去除空格，全部转换为小写  
        String expression=expr.replaceAll("\\s+","");  
        if(expression.isEmpty()){return 0;}  
        //匹配加减乘除，数字，括号，小数点，阶乘符号还有函数符号，如sin,cos,tan,ln  
        Pattern allowChars=Pattern.compile("^[Πe\\d+\\-×÷^!().]+$");  
        boolean hasValidChars =allowChars.matcher(expression).matches();  
        boolean hasFunctions =Pattern.compile("√|sin|cos|tan|ln|log").matcher(expression).find();  
        if(!hasFunctions && !hasValidChars){  
            return -1;  
        }  
        //括号匹配检查 1代表括号不匹配  
        Stack<Character> stack=new Stack<>();  
        for(char c:expression.toCharArray()){  
            if(c=='('){  
                stack.push(c);  
            }else if(c==')'){  
                if(stack.isEmpty()){return 1;}  
                stack.pop();  
            }  
        }  
        if (!stack.isEmpty()) return 1;  
        //匹配紧跟着的符号不为(的函数字符串 返回2表示函数有误  
        Pattern funcPattern=Pattern.compile("(sin|cos|tan|ln|log)(?![(])");  
        if(funcPattern.matcher(expression).find()){  
            return 2;  
        }  
        //运算符连续检查 运算符不能在前面不能没有数字或者是右括号，运算符不能连续出现，返回3代表运算符错误  
        Pattern operatorPattern=Pattern.compile("(?<![Π\\d)])[+×÷^]|[+\\-×÷^]{2,}");  
        if(operatorPattern.matcher(expression).find())return 3;  
        //小数点检查,同一个数中不能出现多个小数点，返回4代表小数点错误  
        Pattern decimalPattern=Pattern.compile("\\.\\d*\\.|[+-÷×^]\\.|\\d+\\.\\d+\\.");  
        if(decimalPattern.matcher(expression).find()) return 4;  
        //阶乘符号验证 !只能在数字，右括号和!之后 返回5代表阶乘错误  
        Pattern factorialPattern=Pattern.compile("(?<![\\d!)])!");  
        if(factorialPattern.matcher(expression).find()) return 5;  
        //字符串首尾验证  
        Pattern startWith =Pattern.compile("^[\\d(-√Π]|^(sin|cos|tan|ln|log)");  
        Pattern endWith =Pattern.compile("[\\d)!]$");  
        if(!startWith.matcher(expression).find()||  
        !endWith.matcher(expression).find()){  
            return 6;  
        }  
        return 0;  
    }  
  
    public static void main(String[] args) {  
        check a = new check();  
        int b = a.Check("Π");  
        System.out.println(b);  
    }  
}
```