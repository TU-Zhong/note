```java
package Calculator;  
  
import java.util.Deque;  
import java.util.LinkedList;  
import java.util.List;  
  
public class convertToAnswer {  
  
    public static double evaluate(List<String> rpn)throws Exception{  
        Deque<Double> stack=new LinkedList<>();  
        for(String token:rpn){  
            if(isNumber(token)){  
                if(token.equals("e")){  
                    stack.push(Math.E);  
                }else if(token.equals("Π")){  
                    stack.push(Math.PI);  
                }else{  
                    stack.push(Double.parseDouble(token));  
                }  
            }else{  
                switch (token){  
                    case "+":  
                        applyBinaryOp(stack,(a,b)-> a+b);  
                        break;  
                    case "-":  
                        applyBinaryOp(stack,(a,b)-> a-b);  
                        break;  
                    case "×":  
                        applyBinaryOp(stack,(a,b)-> a*b);  
                        break;  
                    case "÷":  
                        applyBinaryOp(stack,(a,b)-> a/b);  
                        break;  
                    case "%":  
                        applyBinaryOp(stack,(a,b)-> a%b);  
                        break;  
                    case "√":  
                        applyUnaryOp(stack,a->Math.pow(a,1.0/2));  
                        break;  
                    case "^":  
                        applyBinaryOp(stack,Math::pow);  
                        break;  
                    case "sin":  
                        applyUnaryOp(stack,a->Math.sin(Math.toRadians(a)));  
                        break;  
                    case "cos":  
                        applyUnaryOp(stack,a->Math.cos(Math.toRadians(a)));  
                        break;  
                    case "tan":  
                        applyUnaryOp(stack,a->Math.tan(Math.toRadians(a)));  
                        break;  
                    case "!":  
                        applyUnaryOp(stack,convertToAnswer::factorial);  
                        break;  
                    case "ln":  
                        applyUnaryOp(stack, Math::log);  
                        break;  
                    case "log":  
                        applyUnaryOp(stack,Math::log10);  
                        break;  
                    default:  
                        throw new Exception("未知运算符+"+token);  
                }  
            }  
        }  
        if(stack.size()!=1){  
            throw new Exception("表达式不合法！");  
        }  
        return stack.pop();  
    }  
    private static double factorial(double a)throws Exception{  
        if (a<0||a!=(int)a){  
            throw new Exception("阶乘需要非负整数"+a);  
        }  
        double result=1;  
        for(int i=2;i<=(int)a;i++){  
            result*=i;  
        }  
        return result;  
    }  
    private static void applyBinaryOp(Deque<Double> stack,BinaryOperator op) throws Exception {  
        if(stack.size()<2){  
            throw new Exception("操作数不足！");  
        }  
        double a=stack.pop();  
        double b=stack.pop();  
        stack.push(op.apply(b,a));  
    }  
    private static void applyUnaryOp(Deque<Double> stack,UnaryOperator op)throws Exception{  
        if(stack.isEmpty()){  
            throw new Exception("操作数不足！");  
        }  
        double a=stack.pop();  
        stack.push(op.apply(a));  
    }  
    @FunctionalInterface  
    interface BinaryOperator{  
        double apply(double a,double b);  
    }  
    @FunctionalInterface  
    interface UnaryOperator{  
        double apply(double a) throws Exception;  
    }  
    public static boolean isNumber(String token) {  
        return token.matches("-?\\d+(\\.\\d+)?|e|Π");  
    }  
}
```