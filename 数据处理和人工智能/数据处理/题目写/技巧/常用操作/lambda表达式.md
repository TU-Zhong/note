好的，我们来详细讲解一下 Python 中 `lambda` 表达式的用法和范例。

### 什么是 Lambda 表达式？

`lambda` 表达式，也称为**匿名函数**，是 Python 中一种用来创建简单、小型函数的简洁方式。

可以把它想象成一个“一次性”或“用完即弃”的函数。你不需要使用 `def` 关键字来给它命名，可以直接在需要它的地方定义和使用。

### 基本语法

`lambda` 表达式的语法非常简单，只包含一行：

Python

```
lambda arguments: expression
```

- **`lambda`**：这是声明一个 `lambda` 表达式的关键字。
    
- **`arguments`**：函数的参数，可以有零个、一个或多个，参数之间用逗号 `,` 分隔。
    
- **`:`**：冒号，用来分隔参数和表达式。
    
- **`expression`**：一个**单一的表达式**。这个表达式的计算结果就是函数的返回值。
    

**核心特点：**

1. **匿名**：它没有正式的名称。
    
2. **简洁**：函数体只有一个表达式，代码非常紧凑。
    
3. **限制**：表达式部分不能包含复杂的语句，如 `for` 循环、`while` 循环或多行 `if-else`（但可以使用三元运算符）。
    

---

### 用法与范例

`lambda` 最强大的地方在于它可以作为参数传递给其他高阶函数（例如 `sorted`, `map`, `filter` 等）。

#### 范例 1：基础用法

最简单的 `lambda` 就像一个迷你的数学公式。

Python

```
# 使用 def 定义一个加法函数
def add(x, y):
    return x + y

# 使用 lambda 定义一个等效的加法函数
add_lambda = lambda x, y: x + y

print(add(5, 3))         # 输出: 8
print(add_lambda(5, 3))  # 输出: 8
```

在这个例子中，`lambda x, y: x + y` 创建了一个函数，它接收两个参数 `x` 和 `y`，并返回它们的和。虽然可以像这样给 `lambda` 赋值，但这通常不被推荐，因为直接用 `def` 会更清晰。

#### 范例 2：在 `sorted()` 函数中作为 `key` 使用

这是 `lambda` 最常见的用途之一：为排序提供一个临时的、简单的排序规则。

假设我们有一个学生列表，每个学生是一个字典，我们想根据他们的年龄来排序。

Python

```
students = [
    {'name': '张三', 'age': 20},
    {'name': '李四', 'age': 18},
    {'name': '王五', 'age': 22}
]

# 使用 lambda 指定排序的键 (key) 是每个字典的 'age' 值
sorted_students = sorted(students, key=lambda student: student['age'])

print(sorted_students)
# 输出: [{'name': '李四', 'age': 18}, {'name': '张三', 'age': 20}, {'name': '王五', 'age': 22}]
```

在这里，`lambda student: student['age']` 为 `sorted` 函数提供了一个“取值”规则：对于列表中的每一个元素（`student` 字典），都返回它的 `'age'` 值，`sorted` 函数就根据这个返回的年龄来进行排序。

#### 范例 3：配合 `map()` 函数使用

`map()` 函数会将一个函数应用到可迭代对象的每一个元素上。`lambda` 在这里可以非常方便地定义这个应用的“操作”。

Python

```
numbers = [1, 2, 3, 4, 5]

# 使用 lambda 和 map 计算列表中每个数字的平方
squared_numbers = map(lambda x: x * x, numbers)

print(list(squared_numbers))  # map 返回的是一个迭代器，需要用 list() 转换
# 输出: [1, 4, 9, 16, 25]
```

#### 范例 4：配合 `filter()` 函数使用

`filter()` 函数会根据一个函数的返回值（`True` 或 `False`）来过滤一个可迭代对象。

Python

```
numbers = [1, 2, 3, 4, 5, 6, 7, 8]

# 使用 lambda 和 filter 筛选出列表中的所有偶数
even_numbers = filter(lambda x: x % 2 == 0, numbers)

print(list(even_numbers)) # filter 也返回一个迭代器
# 输出: [2, 4, 6, 8]
```

`lambda x: x % 2 == 0` 这个函数对每个数字进行判断，如果能被 2 整除（即为偶数），则返回 `True`，该数字就会被 `filter` 保留下来。

#### 范例 5：包含条件判断（三元运算符）

虽然 `lambda` 不能使用完整的 `if-else` 语句，但可以使用 Python 的三元运算符 `(value_if_true if condition else value_if_false)` 来实现简单的条件逻辑。

Python

```
# 创建一个函数，如果数字大于 10，则返回 '大'，否则返回 '小'
check_size = lambda x: '大' if x > 10 else '小'

print(check_size(15)) # 输出: 大
print(check_size(5))  # 输出: 小
```

---

### 总结

`lambda` 表达式是 Python 提供的一个便捷工具，用于快速定义功能单一的匿名函数。它特别适合用作那些需要函数作为参数的内置函数的“临时工”，能让你的代码更加紧凑和灵活。当你需要一个简单、一次性的函数时，不妨考虑使用 `lambda`。