下午好！在台北的周四下午，我们来深入这个细节。

这是一个非常好的问题，它触及了这几个集合类设计的核心差异。首先，我们需要澄清一个非常重要的点：

**`LinkedHashSet` 是不支持传入 `Comparator` 的。**

- **原因**：`LinkedHashSet` 的设计目标是**唯一**且**严格地**维护元素的**插入顺序**。这个顺序是由其内部的一个双向链表来保证的。它的“有序”指的是“按插入顺序有序”，而不是“按元素大小排序”。因此，它没有提供使用 `Comparator` 进行排序的功能。
    

**自定义排序功能是 `TreeSet` 的专属特性。**

下面，我们详细讲解如何为 `TreeSet` 传入 `Comparator` 来实现自定义排序。

---

### 如何为 `TreeSet` 传入 `Comparator`？

当存入 `TreeSet` 的元素本身没有实现 `Comparable` 接口（即不具备“自然排序”能力），或者我们想提供一种**不同于其自然排序**的“特殊排序”规则时，就必须在创建 `TreeSet` 时给它提供一个 `Comparator`。

场景准备：

我们先定义一个 Person 类，它没有实现 Comparable 接口。

Java

```
class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + '}';
    }
}
```

如果我们直接尝试 `new TreeSet<Person>()` 并添加 `Person` 对象，程序会在运行时抛出 `ClassCastException`，因为它不知道该如何比较两个 `Person` 对象。

现在，我们希望可以按照**年龄**来对 `Person` 对象进行排序。

#### 方式一：使用独立的比较器类（传统写法）

这是最经典、最完整的写法。

Java

```
import java.util.Comparator;
import java.util.Set;
import java.util.TreeSet;

// 1. 创建一个实现了 Comparator 接口的独立类
class AgeComparator implements Comparator<Person> {
    @Override
    public int compare(Person p1, Person p2) {
        // 返回负数表示 p1 小于 p2
        // 返回 0 表示相等
        // 返回正数表示 p1 大于 p2
        return p1.getAge() - p2.getAge(); // 按年龄升序排序
    }
}

public class TreeSetComparatorExample {
    public static void main(String[] args) {
        // 2. 在创建 TreeSet 时，将比较器实例传入构造方法
        Set<Person> personSet = new TreeSet<>(new AgeComparator());

        personSet.add(new Person("张三", 30));
        personSet.add(new Person("李四", 20));
        personSet.add(new Person("王五", 25));

        // TreeSet 会自动根据 AgeComparator 的规则进行排序
        personSet.forEach(System.out::println);
    }
}
```

**输出（已按年龄升序）：**

```
Person{name='李四', age=20}
Person{name='王五', age=25}
Person{name='张三', age=30}
```

#### 方式二：使用匿名内部类（Java 8 之前常用）

如果你这个比较器只用一次，不想再单独创建一个文件，可以使用匿名内部类。

Java

```
Set<Person> personSet = new TreeSet<>(new Comparator<Person>() {
    @Override
    public int compare(Person p1, Person p2) {
        return p1.getAge() - p2.getAge();
    }
});
// ... 添加元素 ...
```

这种写法比方式一简洁，但在 Java 8 之后有了更好的替代品。

#### 方式三：使用 Lambda 表达式（Java 8+ 推荐）

由于 `Comparator` 是一个**函数式接口**（只有一个抽象方法 `compare`），我们可以用 Lambda 表达式来极大地简化代码。

Java

```
// 直接将比较逻辑作为 Lambda 表达式传入
Set<Person> personSet = new TreeSet<>((p1, p2) -> p1.getAge() - p2.getAge());

personSet.add(new Person("张三", 30));
personSet.add(new Person("李四", 20));

personSet.forEach(System.out::println);
```

#### 方式四：使用 `Comparator` 的静态辅助方法（Java 8+ 最优雅）

Java 8 为 `Comparator` 接口提供了很多非常好用的静态方法，让创建比较器变得更加简单和易读。

**1. 按年龄排序：**

Java

```
// Comparator.comparingInt() 接收一个函数，该函数返回一个用于排序的 int 值
// Person::getAge 是一个方法引用，等同于 p -> p.getAge()
Comparator<Person> byAge = Comparator.comparingInt(Person::getAge);
Set<Person> personSet = new TreeSet<>(byAge);
```

2. 复合排序：先按年龄，再按名字

如果年龄相同，还想按名字的字母顺序排序，可以使用 thenComparing()。

Java

```
Comparator<Person> byAgeThenByName = Comparator.comparingInt(Person::getAge)
                                               .thenComparing(Person::getName);

Set<Person> personSet = new TreeSet<>(byAgeThenByName);
```

### 总结

- **`LinkedHashSet`** 只关心**插入顺序**，**不能**使用 `Comparator` 进行排序。
    
- **`TreeSet`** 专门用于**排序**，它在创建时**可以**接收一个 `Comparator` 对象作为其排序规则。
    
- 如果存入 `TreeSet` 的对象没有实现 `Comparable`，则**必须**在构造时提供 `Comparator`。
    
- 在现代 Java 开发（Java 8+）中，**强烈推荐使用 Lambda 表达式或 `Comparator` 的静态辅助方法**（如 `comparingInt`, `thenComparing`）来创建比较器，它们代码更少、意图更清晰、也更灵活。