在 Java 泛型中，`?` 和 `T` 都是用来表示未知类型的，但它们的用途和含义有显著的区别。以下是它们的详细对比：

---

### 1. **`T` 是什么？**
- `T` 是一个**具体类型参数**，通常用于定义泛型类、泛型接口或泛型方法。
- `T` 是一个占位符，代表一个具体的类型，在**使用时需要明确指定**。
- `T` 的作用范围通常局限于单个类、接口或方法。

#### 例子：
```java
class Box<T> {
    private T item;

    public void setItem(T item) {
        this.item = item;
    }

    public T getItem() {
        return item;
    }
}

Box<String> stringBox = new Box<>();
stringBox.setItem("Hello");
System.out.println(stringBox.getItem()); // 输出: Hello
```

**解释：**
- `T` 在 `Box` 类中代表一个具体的类型，比如 `String`、`Integer` 等。
- 使用时需要明确指定 `T` 的类型。

---

### 2. **`?` 是什么？**
- `?` 是一个**通配符**，用于表示**未知类型**。
- `?` 通常用于方法的参数或返回值中，表示“任意类型”。
- `?` 不能直接用于定义泛型类或方法。

#### 例子：
```java
public static void printList(List<?> list) {
    for (Object item : list) {
        System.out.println(item);
    }
}

List<String> stringList = Arrays.asList("A", "B", "C");
List<Integer> integerList = Arrays.asList(1, 2, 3);

printList(stringList);  // 输出: A B C
printList(integerList); // 输出: 1 2 3
```

**解释：**
- `List<?>` 表示一个未知类型的列表，可以接受任何类型的 `List`。
- `?` 的作用是让方法更通用，能够处理多种类型的数据。

---

### 3. **`T` 和 `?` 的区别**
| **特性**               | **`T`**                                  | **`?`**                                  |
|------------------------|-----------------------------------------|-----------------------------------------|
| **用途**               | 用于定义泛型类、接口或方法。              | 用于表示未知类型，通常用于方法参数或返回值。 |
| **类型是否明确**       | 类型在定义时明确（使用时需要指定）。       | 类型未知，可以表示任意类型。              |
| **是否可以实例化**     | 可以实例化，如 `new Box<T>()`。           | 不能直接实例化，如 `new Box<?>()` 是错误的。 |
| **是否可以调用方法**   | 可以调用 `T` 类型的方法。                 | 只能调用 `Object` 类型的方法（因为类型未知）。 |
| **是否可以用于定义类** | 可以用于定义泛型类、接口或方法。           | 不能直接用于定义泛型类或方法。             |

---

### 4. **`?` 的高级用法：上下界通配符**
`?` 可以与 `extends` 和 `super` 结合，用于限制通配符的范围。

#### 例子 1：上限通配符（`? extends T`）
```java
public static void printNumbers(List<? extends Number> list) {
    for (Number number : list) {
        System.out.println(number);
    }
}

List<Integer> integerList = Arrays.asList(1, 2, 3);
List<Double> doubleList = Arrays.asList(1.1, 2.2, 3.3);

printNumbers(integerList); // 输出: 1 2 3
printNumbers(doubleList);  // 输出: 1.1 2.2 3.3
```

**解释：**
- `List<? extends Number>` 表示一个类型为 `Number` 或其子类的列表。

#### 例子 2：下限通配符（`? super T`）
```java
public static void addNumbers(List<? super Integer> list) {
    list.add(1);
    list.add(2);
}

List<Number> numberList = new ArrayList<>();
addNumbers(numberList);
System.out.println(numberList); // 输出: [1, 2]
```

**解释：**
- `List<? super Integer>` 表示一个类型为 `Integer` 或其父类的列表。

---

### 5. **`T` 和 `?` 的对比总结**
| **场景**               | **`T`**                                  | **`?`**                                  |
|------------------------|-----------------------------------------|-----------------------------------------|
| **定义泛型类**         | `class Box<T> { ... }`                  | 不能使用 `?` 定义泛型类。                |
| **定义泛型方法**       | `<T> void print(T item) { ... }`        | 不能使用 `?` 定义泛型方法。              |
| **方法参数**           | `void print(T item)`                    | `void printList(List<?> list)`          |
| **类型限制**           | `<T extends Number>`                    | `List<? extends Number>`               |
| **实例化**             | `Box<String> box = new Box<>();`        | `Box<?> box = new Box<String>();`       |

---

### 6. **什么时候用 `T`，什么时候用 `?`？**
- 用 `T` 的情况：
  - 定义泛型类、接口或方法。
  - 需要明确指定类型，并在代码中使用该类型的方法或属性。
- 用 `?` 的情况：
  - 处理未知类型，通常用于方法参数或返回值。
  - 需要让方法更通用，能够处理多种类型的数据。

---

### 总结
- `T` 是一个具体的类型参数，用于定义泛型类、接口或方法。
- `?` 是一个通配符，用于表示未知类型，通常用于方法参数或返回值。
- 如果你需要定义泛型结构，用 `T`；如果你需要处理未知类型，用 `?`。