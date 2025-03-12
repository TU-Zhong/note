

在 Java 中，`Matcher` 类是正则表达式匹配操作的核心工具，用于对字符串进行模式匹配、信息提取和替换操作。以下是其常用操作及示例：

---

### **1. 核心方法说明**
| 方法 | 描述 | 是否常用 |
|------|------|----------|
| `matches()` | **完全匹配**：检查整个字符串是否符合正则表达式。 | ✔️ |
| `find()` | **查找子串**：查找字符串中**下一个**符合正则的子串。 | ✔️ |
| `group()` | **获取匹配内容**：返回当前匹配的子串或捕获组。 | ✔️ |
| `start()` / `end()` | **获取匹配位置**：返回匹配子串的起始/结束索引（不含结束字符）。 | ✔️ |
| `replaceAll()` / `replaceFirst()` | **替换操作**：替换匹配的子串。 | ✔️ |
| `lookingAt()` | **部分匹配**：检查字符串的**开头部分**是否符合正则（非完全匹配）。 | ❌ |
| `reset()` | **重置匹配器**：重置状态以复用匹配器对象。 | ❌ |

---

### **2. 常用操作详解**
#### **(1) `matches()`：完全匹配验证**
验证整个字符串是否完全符合正则表达式。
```java
String regex = "\\d{3}-\\d{4}";
Matcher matcher = Pattern.compile(regex).matcher("123-4567");
boolean isValid = matcher.matches(); // true
```

---

#### **(2) `find()`：循环查找子串**
查找字符串中所有符合正则的子串。
```java
String text = "Prices are $100 and $200.";
Matcher matcher = Pattern.compile("\\$\\d+").matcher(text);
while (matcher.find()) {
    System.out.println(matcher.group()); // $100, $200
}
```

---

#### **(3) `group()`：提取匹配内容**
返回当前匹配的子串，或指定捕获组内容。
```java
String date = "2023-10-05";
Matcher matcher = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})").matcher(date);
if (matcher.find()) {
    String year = matcher.group(1);  // 2023
    String month = matcher.group(2); // 10
    String day = matcher.group(3);   // 05
}
```
- **`group(0)`**：整个匹配的子串（如 `2023-10-05`）。
- **`group(n)`**：第 `n` 个捕获组（从1开始）。

---

#### **(4) `start()` / `end()`：获取匹配位置**
获取匹配子串在原始字符串中的位置。
```java
String text = "Hello World!";
Matcher matcher = Pattern.compile("World").matcher(text);
if (matcher.find()) {
    int start = matcher.start(); // 6
    int end = matcher.end();     // 11（索引 11 不包含在内）
    String sub = text.substring(start, end); // "World"
}
```

---

#### **(5) 替换操作：`replaceAll()` / `replaceFirst()`**
替换匹配的子串。
```java
String text = "1a2b3c";
String replacedAll = text.replaceAll("\\D", "#");    // "1#2#3#"
String replacedFirst = text.replaceFirst("\\D", "#");// "1#2b3c"
```

---

### **3. 完整示例：提取并格式化日期**
```java
import java.util.regex.*;

public class MatcherExample {
    public static void main(String[] args) {
        String text = "Dates: 2023-10-05, 2024/12/31, invalid-date";
        String regex = "(\\d{4})[-/](\\d{2})[-/](\\d{2})";
        
        Matcher matcher = Pattern.compile(regex).matcher(text);
        while (matcher.find()) {
            String year = matcher.group(1);
            String month = matcher.group(2);
            String day = matcher.group(3);
            String formatted = String.format("%s年%s月%s日", year, month, day);
            System.out.println(formatted);
        }
    }
}
```

**输出**：
```
2023年10月05日
2024年12月31日
```

---

### **4. 注意事项**
1. **调用顺序**：在调用 `group()`、`start()`、`end()` 前必须先调用 `matches()` 或 `find()`，否则会抛出 `IllegalStateException`。
2. **分组索引**：分组索引从 `1` 开始（`group(0)` 为整个匹配的子串）。
3. **性能优化**：预编译 `Pattern` 以提高性能（尤其重复使用时）。

---

### **总结**
`Matcher` 类的常用操作包括：
1. **验证**：`matches()`
2. **查找**：`find()`
3. **提取**：`group()` + 捕获组
4. **替换**：`replaceAll()` / `replaceFirst()`
5. **位置获取**：`start()` / `end()`

掌握这些方法后，可以灵活处理文本匹配、数据清洗和结构化提取等场景。