```java
package Calculator;  
  
import java.util.*;  
import java.util.regex.Matcher;  
import java.util.regex.Pattern;  
  
public class find {  
    private static final HashMap<String, Integer> PRECEDENCE = new HashMap<>();  
  
    static {  
        PRECEDENCE.put("(", -1);  
        PRECEDENCE.put("+", 1);  
        PRECEDENCE.put("-", 1);  
        PRECEDENCE.put("×", 2);  
        PRECEDENCE.put("÷", 2);  
        PRECEDENCE.put("%", 2);  
        PRECEDENCE.put("^", 4);  
        PRECEDENCE.put("√", 4);  
        PRECEDENCE.put("!", 5);  
    }  
  
    private static final Set<String> FUNCTIONS = Set.of("sin", "cos", "tan", "log", "ln");  
  
    public List<String> getChar(String str) {  
        //"(\\d.?\\d*Π)|(+\\-×÷^%)|(tan|sin|cos|ln|!|e)"  
        Pattern pattern = Pattern.compile("(-)?(\\d+\\.\\d+|\\d+|Π|e)|([()+\\-÷×^%!√])|(tan|sin|cos|ln|log)");  
        Matcher matcher = pattern.matcher(str);  
        List<String> result = new ArrayList<>();  
        while (matcher.find()) {  
            result.add(matcher.group());  
        }  
        return result;  
    }  
  
    public List<String> infixToRPN(List<String> tokens) throws Exception {  
        List<String> output = new ArrayList<>();  
        Deque<String> stack = new LinkedList<>();  
        for (String token : tokens) {  
            if (isNumber(token)) {  
                output.add(token);  
            } else if (FUNCTIONS.contains(token)) {  
                stack.push(token);  
            } else if (token.equals("(")) {  
                stack.push("(");  
            } else if (token.equals(")")) {  
                while (!stack.isEmpty() && !stack.peek().equals("(")) {  
                    output.add(stack.pop());  
                }  
                if (stack.isEmpty()) {  
                    throw new Exception("括号不匹配！");  
                }  
                stack.pop();  
                if (!stack.isEmpty() && FUNCTIONS.contains(stack.peek())) {  
                    output.add(stack.pop());  
                }  
            } else {  
                while (!stack.isEmpty() &&  
                        (comparePrecedence(stack.peek(), token) > 0 ||  
                                comparePrecedence(stack.peek(), token) == 0 && isLeftAssociated(token))) {  
                    output.add(stack.pop());  
                }  
                stack.push(token);  
            }  
        }  
        while (!stack.isEmpty()) {  
            output.add(stack.pop());  
        }  
        return output;  
    }  
  
    public boolean isLeftAssociated(String token) {  
        return !token.equals("^") && !token.equals("!");  
    }  
  
    public int comparePrecedence(String stackOp, String currentOp) {  
        int stackPre = PRECEDENCE.getOrDefault(stackOp, -1);  
        int currentPre = PRECEDENCE.getOrDefault(currentOp, -1);  
        if (stackOp.equals("^") && currentOp.equals("^")) {  
            return -1;  
        }  
        return Integer.compare(stackPre, currentPre);  
    }  
  
    public boolean isNumber(String token) {  
        return token.matches("-?\\d+(\\.\\d+)?|e|Π");  
    }  
  
    /*  
     *校验例子  
     * 3 + 4 * 2     * (3 + 4) * 2     * 2^3^2     * 5! + 3     * sin(30 + 45)     * 3 + 4 * 2 / (1 - 5)^2     * -5 + 3 * 2     * 1.5*e^2 + 3     * log(100) * 2 + ln(e^3)     * (5! / (3 * 2)) ^ 2     */    public double finalResult(find test,String str) throws Exception {  
        List<String> chars = test.getChar(str);  
        List<String> RPN = test.infixToRPN(chars);  
        return convertToAnswer.evaluate(RPN);  
    }  
    public static void main(String[] args) throws Exception {  
        find test = new find();  
//        String[] strs = {  
//                "3 + 4 × 2",  
//                "(3 + 4) × 2",  
//                "2^3^2",  
//                "5! + 3",  
//                "sin(30 + 45)",  
//                "3 + 4 × 2 ÷ (1 - 5)^2",  
//                "-5 + 3 × 2",  
//                "1.5×e^2 + 3",  
//                "log(100) × 2 + ln(e^3)",  
//                "(5! ÷ (3 × 2)) ^ 2"};  
        String[] strs={"Π+5"};  
        for (String str : strs) {  
            System.out.println("这是原来的字符串" + str);  
            List<String> chars = test.getChar(str);  
            List<String> RPN = test.infixToRPN(chars);  
            double result=convertToAnswer.evaluate(RPN);  
            System.out.println("这是运算结果："+result);  
            System.out.print("这是现在的字符串");  
            for (String s : RPN) {  
                System.out.print(s);  
            }  
            System.out.println();  
            System.out.println("---------------------------------");  
        }  
  
    }  
}
```