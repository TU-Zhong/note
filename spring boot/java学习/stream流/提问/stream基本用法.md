**Stream API** 是 Java 8 引入的一个强大工具，主要用于对集合（如 `List`、`Set`、`Map` 等）进行 **高效、声明式的处理**。它特别适合对数据进行 **过滤、转换、聚合** 等操作。以下是 Stream API 的主要应用场景：

---

### 1. **集合数据处理**
[[集合数据处理基本用法]]
Stream API 最常见的用途是对集合中的数据进行处理，比如：
- **过滤**：筛选符合条件的元素。
- **映射**：将元素转换为另一种形式。
- **排序**：对元素进行排序。
- **去重**：去除重复元素。
- **分组**：将元素按条件分组。

#### 示例：
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David", "Alice");

// 过滤长度大于 3 的名字，去重，转为大写，排序
List<String> result = names.stream()
                           .filter(name -> name.length() > 3)
                           .distinct()
                           .map(String::toUpperCase)
                           .sorted()
                           .collect(Collectors.toList());

System.out.println(result); // 输出 [ALICE, CHARLIE, DAVID]
```

---

### 2. **聚合操作**
Stream API 提供了丰富的聚合方法，用于对集合中的数据进行统计或计算：
- **计数**：`count()`
- **求和**：`sum()`
- **平均值**：`average()`
- **最大值**：`max()`
- **最小值**：`min()`

#### 示例：
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// 计算总和
int sum = numbers.stream().mapToInt(Integer::intValue).sum();
System.out.println("总和: " + sum); // 输出 15

// 计算平均值
OptionalDouble average = numbers.stream().mapToInt(Integer::intValue).average();
average.ifPresent(avg -> System.out.println("平均值: " + avg)); // 输出 3.0
```

---

### 3. **并行处理**
Stream API 支持 **并行流**（`parallelStream`），可以充分利用多核 CPU 的优势，提高数据处理效率。

#### 示例：
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// 并行计算总和
int sum = numbers.parallelStream().mapToInt(Integer::intValue).sum();
System.out.println("并行总和: " + sum); // 输出 55
```

---

### 4. **文件处理**
Stream API 可以用于处理文件中的每一行数据，比如读取文件内容、过滤行、统计行数等。

#### 示例：
```java
import java.io.IOException;
import java.nio.file.Files;
import java.nio.file.Paths;

public class FileStreamDemo {
    public static void main(String[] args) throws IOException {
        // 读取文件，统计行数
        long lineCount = Files.lines(Paths.get("example.txt")).count();
        System.out.println("文件行数: " + lineCount);
    }
}
```

---

### 5. **数据库查询结果处理**
Stream API 可以与数据库查询结果结合，对查询到的数据进行进一步处理。

#### 示例：
```java
List<Person> people = // 从数据库查询到的数据

// 过滤年龄大于 30 的人，按姓名排序
List<Person> result = people.stream()
                            .filter(person -> person.getAge() > 30)
                            .sorted(Comparator.comparing(Person::getName))
                            .collect(Collectors.toList());
```

---

### 6. **无限流**
Stream API 支持生成 **无限流**（`Stream.iterate` 或 `Stream.generate`），可以用于生成无限序列或随机数。

#### 示例：
```java
// 生成斐波那契数列
Stream.iterate(new int[]{0, 1}, t -> new int[]{t[1], t[0] + t[1]})
      .limit(10)
      .forEach(t -> System.out.println(t[0]));

// 生成随机数
Stream.generate(Math::random)
      .limit(5)
      .forEach(System.out::println);
```

---

### 7. **自定义操作**
Stream API 支持通过 `reduce` 和 `collect` 方法实现自定义的聚合操作。

#### 示例：
```java
List<String> words = Arrays.asList("Hello", "World", "Java", "Stream");

// 将所有单词拼接成一个字符串
String result = words.stream().reduce("", (a, b) -> a + " " + b).trim();
System.out.println(result); // 输出 "Hello World Java Stream"
```

---

### 8. **与 Optional 结合**
Stream API 的 `findFirst`、`findAny` 等方法返回 `Optional`，可以方便地处理可能为空的结果。

#### 示例：
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// 查找第一个以 "A" 开头的名字
Optional<String> result = names.stream()
                               .filter(name -> name.startsWith("A"))
                               .findFirst();

result.ifPresent(name -> System.out.println("找到: " + name)); // 输出 "找到: Alice"
```

---

### 9. **与 Lambda 表达式结合**
Stream API 与 Lambda 表达式结合，可以让代码更简洁、易读。

#### 示例：
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// 筛选并打印长度大于 3 的名字
names.stream()
     .filter(name -> name.length() > 3)
     .forEach(System.out::println); // 输出 "Alice", "Charlie"
```

---

### 总结
Stream API 主要应用于以下场景：
1. **集合数据处理**（过滤、映射、排序、去重、分组等）。
2. **聚合操作**（计数、求和、平均值、最大值、最小值等）。
3. **并行处理**（利用多核 CPU 提高效率）。
4. **文件处理**（读取文件、过滤行、统计行数等）。
5. **数据库查询结果处理**（对查询到的数据进行进一步处理）。
6. **无限流**（生成无限序列或随机数）。
7. **自定义操作**（通过 `reduce` 和 `collect` 实现自定义聚合）。
8. **与 Optional 结合**（处理可能为空的结果）。
9. **与 Lambda 表达式结合**（让代码更简洁、易读）。

Stream API 的优势在于：
- **声明式编程**：只需告诉程序“做什么”，而不是“怎么做”。
- **链式调用**：可以流畅地组合多个操作。
- **并行支持**：轻松实现并行处理，提升性能。

通过掌握 Stream API，可以大幅提高 Java 程序的处理效率和代码可读性！