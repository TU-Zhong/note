好的，这是一个非常经典且重要的 MyBatis 映射问题。`ofType` 和 `javaType` 在 `<collection>` 标签中的作用不同，理解它们的区别是掌握 MyBatis 关联关系映射的关键。

简单来说，这是一个 **“容器里的东西”** 和 **“容器本身”** 的区别。

- **`ofType`**: 指定**集合中元素**的类型 (The type **OF** the elements **IN** the collection)。
- **`javaType`**: 指定**集合本身**的类型 (The **Java Type** of the collection property **ITSELF**)。

下面我们来详细拆解。

---
[[javaType 集合非默认实现类要写]]
### `ofType` (元素的类型)

`ofType` 是 `<collection>` 和 `<association>` 标签特有的属性。它告诉 MyBatis，这个集合属性（比如一个 `List`）里面应该存放**哪种类型的对象**。

在你的例子中：

XML

```
<collection property="flavors" ofType="com.sky.entity.DishFlavor">
    ...
</collection>
```

这行代码的含义是：

1. **`property="flavors"`**: 映射到主对象（比如 `Dish` 类）的 `flavors` 属性上。
2. **`ofType="com.sky.entity.DishFlavor"`**: **这是关键**。它告诉 MyBatis：“`flavors` 这个集合里面，存放的每一个元素都应该是 `com.sky.entity.DishFlavor` 类的实例。”

MyBatis 在处理查询结果时，会为关联查询到的每一行口味数据（flavor row）创建一个 `DishFlavor` 对象，然后将这些对象添加到现在正在构建的 `flavors` 集合中。

**`ofType` 是必须的**，因为 MyBatis 需要知道如何将数据库返回的 `flavor_id`, `flavors_name` 等列映射成一个具体的 Java 对象。

---

### `javaType` (集合本身的类型)

`javaType` 是一个通用属性，可以用在 `<result>`, `<parameter>`, `<collection>` 等很多标签上。它用于**明确指定属性本身的 Java 类型**。

当用在 `<collection>` 标签上时，`javaType` 指定的是**集合的实现类**。

我们来看一个例子。假设你的 `Dish` 类是这样定义的：

Java

```
public class Dish {
    private Integer id;
    private String name;
    // a list of flavors
    private List<DishFlavor> flavors; 
    // getters and setters
}
```

在你的 XML 映射中，MyBatis 通过反射能看到 `flavors` 属性的类型是 `java.util.List`。当 MyBatis 需要实例化这个 `flavors` 属性时，它会为一个接口（`List`）选择一个默认的实现类，通常是 `java.util.ArrayList`。

因此，在大多数情况下，你**不需要**为 `<collection>` 指定 `javaType`。

**那么，什么时候需要使用 `javaType` 呢？**

当你需要使用一个**非默认的集合实现类**时。比如，你希望 `flavors` 是一个 `LinkedList` 或者 `HashSet`。

XML

```
<collection property="flavors" 
            ofType="com.sky.entity.DishFlavor"
            javaType="java.util.LinkedList"> 
    ...
</collection>

<collection property="flavors" 
            ofType="com.sky.entity.DishFlavor"
            javaType="java.util.HashSet">
    ...
</collection>
```

---

### 核心区别总结

|特性|`ofType`|`javaType`|
|:--|:--|:--|
|**作用对象**|集合中的**元素**|集合**本身**（属性）|
|**含义**|"这个List里面装的是`DishFlavor`"|"这个`flavors`属性本身是一个`ArrayList`"|
|**使用场景**|在 `<collection>` 中**几乎总是必须的**，用于指定元素类型。|在 `<collection>` 中**通常是可选的**，仅在需要特定集合实现类时使用。|
|**类比**|`List<Apple>` 中的 `Apple`|`List<Apple>` 中的 `List` (或具体的`ArrayList`)|

Export to Sheets

### 结论

回到你的代码：

XML

```
<collection property="flavors" ofType="com.sky.entity.DishFlavor">
    ...
</collection>
```

这个写法是**完全正确且最常见**的。

- 你通过 `ofType` 明确告诉了 MyBatis，`flavors` 列表里应该填充 `DishFlavor` 对象。
- 你没有写 `javaType`，所以 MyBatis 会自动为你创建一个 `ArrayList` 并赋值给 `flavors` 属性。

如果你写成这样：

XML

```
<collection property="flavors" javaType="java.util.ArrayList">
    ...
</collection>
```

这是**错误**的。MyBatis 知道要创建一个 `ArrayList`，但它不知道这个 `List` 里面应该存放什么类型的对象，也就无法将 `flavor_id` 等列映射到对象的属性上。