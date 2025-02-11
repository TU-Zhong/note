[[接口有不止一个抽象方法怎么办]]
[[]]
### **Q:**
```java
@FunctionalInterface
interface StringProcessor {
    String process(String input);
}

public class Demo {
    public static void main(String[] args) {
        StringProcessor toUpper = s -> s.toUpperCase();
        System.out.println(toUpper.process("hello")); // 输出 "HELLO"
    }
}
```
这里这个toUpper=一个lambda表达式是什么意思？为什么可以这样写？

### **A:**
### 问题核心：
```java
StringProcessor toUpper = s -> s.toUpperCase();
```
这行代码是什么意思？为什么可以用 Lambda 表达式这样写？

---

### 解释：

#### 1. **`StringProcessor` 接口的作用**
`StringProcessor` 是一个 **函数式接口**（Functional Interface），它只有一个抽象方法 `process`：
```java
@FunctionalInterface
interface StringProcessor {
    String process(String input);
}
```
它表示一个“处理器”，可以接受一个 `String` 输入，返回一个 `String` 结果。

---

#### 2. **Lambda 表达式的作用**
Lambda 表达式 `s -> s.toUpperCase()` 的作用是：
- **接受一个参数 `s`**：即 `process` 方法的输入参数。
- **执行 `s.toUpperCase()`**：将输入的字符串转为大写。
- **返回结果**：转换后的字符串。

它实际上是对 `process` 方法的实现。

---

#### 3. **为什么可以这样写？**
因为 `StringProcessor` 是一个函数式接口，它只有一个抽象方法，所以可以用 Lambda 表达式来直接实现这个方法：
- `s -> s.toUpperCase()` 替代了传统匿名内部类的写法：
```java
// 传统匿名内部类写法
StringProcessor toUpper = new StringProcessor() {
    @Override
    public String process(String input) {
        return input.toUpperCase();
    }
};

// Lambda 表达式写法（简化版）
StringProcessor toUpper = s -> s.toUpperCase();
```
Lambda 表达式让代码更简洁，省去了不必要的模板代码。

---

#### 4. **Lambda 表达式的类型推断**
Java 编译器会根据上下文自动推断 Lambda 表达式的类型：
- 因为 `toUpper` 的类型是 `StringProcessor`，所以编译器知道 Lambda 表达式的参数 `s` 是 `String` 类型。
- 因此，`s.toUpperCase()` 是合法的，因为 `s` 是 `String` 类型。

---

#### 5. **总结**
- `toUpper = s -> s.toUpperCase()` 的作用是：创建一个 `StringProcessor` 的实例，其 `process` 方法会将输入的字符串转为大写。
- 可以这样写是因为 `StringProcessor` 是函数式接口，Lambda 表达式可以直接实现其唯一抽象方法。
- Lambda 表达式让代码更简洁，省去了匿名内部类的繁琐写法。

---

### 类比生活中的例子：
假设你有一个任务：“把名字转为大写”。传统方式需要写一张任务清单：
1. 接受一个名字。
2. 把名字转为大写。
3. 返回结果。

但 Lambda 表达式就像直接说：“名字 -> 转为大写”，省去了中间步骤，直接完成任务。

---

### 代码执行流程：
```java
StringProcessor toUpper = s -> s.toUpperCase(); // 定义任务
System.out.println(toUpper.process("hello"));   // 执行任务
```
4. `toUpper` 是一个“处理器”，它的任务是“把字符串转为大写”。
5. `toUpper.process("hello")` 执行任务，将 `"hello"` 转为 `"HELLO"`。
6. 输出结果 `"HELLO"`。

---

通过这种方式，Lambda 表达式让代码更直观、更简洁！