

在 Java 泛型中，**泛型类型参数（如 `T`）** 和 **通配符（`?`）** 的用法和声明位置有明确的规则。以下是详细解释和示例：

---

### **1. 泛型类型参数 `T` 的声明位置**
#### **(1) 泛型方法**
在 **泛型方法** 中，类型参数 `T` 需要声明在 **方法返回类型之前**，紧跟在方法的修饰符（如 `public`、`static`）之后。

**语法：**
```java
修饰符 <T> 返回类型 方法名(参数列表) { ... }
```

**示例：**
```java
// 定义泛型方法：T 声明在返回类型前
public static <T> void printArray(T[] array) {
    for (T element : array) {
        System.out.print(element + " ");
    }
}

// 使用泛型方法
Integer[] intArr = {1, 2, 3};
printArray(intArr); // 输出: 1 2 3
```

---

#### **(2) 泛型类**
在 **泛型类** 中，类型参数 `T` 需要声明在 **类名之后**。

**语法：**
```java
class 类名<T> { ... }
```

**示例：**
```java
// 定义泛型类：T 声明在类名后
class Box<T> {
    private T value;

    public void setValue(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}

// 使用泛型类
Box<String> box = new Box<>();
box.setValue("Hello");
System.out.println(box.getValue()); // 输出: Hello
```

---

### **2. 通配符 `?` 的使用位置**
通配符 `?` **不需要声明**，它用于表示 **未知类型**，主要在以下场景中使用：

#### **(1) 泛型参数类型**
当方法参数需要接受 **任意泛型类型** 的对象时，可以用 `?` 作为通配符。
[[当方法参数需要接受 任意泛型类型的对象时，可以用 问好 作为通配符。请用小孩也能懂的话给我解释解释这句话的意思。为什么这个时候不用T。]]
**示例：**
```java
// 定义一个可以打印任意 List 类型的方法
public static void printList(List<?> list) {
    for (Object element : list) {
        System.out.print(element + " ");
    }
}

// 使用示例
List<String> strList = List.of("A", "B");
List<Integer> intList = List.of(1, 2);

printList(strList); // 输出: A B
printList(intList); // 输出: 1 2
```

---

#### **(2) 通配符的上下界**
通配符 `?` 可以与 `extends` 或 `super` 结合，限定类型的范围：
- `<? extends T>`：表示类型是 `T` 或其子类（**上界通配符**）。
- `<? super T>`：表示类型是 `T` 或其父类（**下界通配符**）。

**示例：**
```java
// 只接受 Number 及其子类（如 Integer、Double）
public static void printNumbers(List<? extends Number> list) {
    for (Number num : list) {
        System.out.print(num + " ");
    }
}

// 使用示例
List<Integer> integers = List.of(1, 2);
List<Double> doubles = List.of(1.1, 2.2);

printNumbers(integers); // 输出: 1 2
printNumbers(doubles);  // 输出: 1.1 2.2
```

---

### **3. `T` 和 `?` 的对比总结**
| 特性                | `T`（泛型类型参数）              | `?`（通配符）                     |
|---------------------|----------------------------------|----------------------------------|
| **声明位置**        | 泛型方法/泛型类中显式声明        | 无需声明，直接使用               |
| **用途**            | 表示具体类型，可以被代码引用     | 表示未知类型，无法直接引用       |
| **适用场景**        | 方法或类需要操作具体类型时       | 方法需要接受多种泛型类型时       |
| **是否支持上下界**  | 是（如 `<T extends Number>`）    | 是（如 `<? extends Number>`）    |

---

### **4. 常见误区**
#### **(1) 通配符 `?` 不能用于泛型方法的定义**
以下代码是错误的：
```java
public static void printList(List<?> list) { ... }
// 这是合法的，但其中的 `?` 是通配符，不是泛型方法的定义！

// 错误示例：试图用通配符声明泛型方法
public static <?> void printList(List<?> list) { ... } // ❌ 语法错误！`
```

#### **(2) 泛型方法 `T` 和通配符 `?` 的混合使用**
```java
public static <T> void merge(List<? extends T> list1, List<? extends T> list2) {
    // 将 list1 和 list2 的内容合并到另一个列表中
}
```

---

### **5. 终极总结**
- **泛型类型参数 `T`**：需要显式声明在方法返回类型前或类名后，表示具体的类型，可以在代码中直接使用。
- **通配符 `?`**：不需要声明，直接用于方法参数或变量中，表示未知类型，增加灵活性但限制操作。

根据需求选择合适的工具，写出更安全、更灵活的泛型代码！ 🚀