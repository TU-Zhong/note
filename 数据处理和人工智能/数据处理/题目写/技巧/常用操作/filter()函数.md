好的，我们来详细介绍一下 `pandas` 中非常实用的 `filter` 方法。

首先，非常重要的一点是：`pandas.DataFrame.filter()` **不同于** Python 内置的 `filter()` 函数。它们的功能和使用场景完全不同。

||`pandas.DataFrame.filter()`|Python 内置 `filter()`|
|---|---|---|
|**作用对象**|DataFrame 的**标签**（行索引名、列名）|任何可迭代对象中的**元素值**|
|**核心功能**|根据名称筛选**行或列**|根据条件筛选**数据值**|

Export to Sheets

简单来说，`pandas` 的 `filter` 是一个**基于名称的筛选工具**，它不关心单元格里的数据是什么，只关心行和列的“名字”。

---

### `pandas.filter()` 的用法

`filter` 方法主要通过三个参数来工作：`items`、`like` 和 `regex`。你一次通常只使用其中一个。

首先，我们创建一个示例 DataFrame 来演示：

Python

```
import pandas as pd

data = {
    '订单编号_A': [101, 102, 103],
    '订单金额_A': [340, 500, 120],
    '客户ID_B': [1, 2, 3],
    '客户评级_B': [5, 4, 5]
}
index_labels = ['行_1', '行_2', '行_3']
df = pd.DataFrame(data, index=index_labels)

print("原始 DataFrame:")
print(df)
```

输出的原始 DataFrame：

```
      订单编号_A  订单金额_A  客户ID_B  客户评级_B
行_1      101      340       1        5
行_2      102      500       2        4
行_3      103      120       3        5
```

默认情况下，`filter` 对**列标签**进行操作 (`axis=1`)。

#### 1. 使用 `items` 参数：精确匹配

`items` 用于筛选出名称**完全匹配**的行或列。你需要提供一个包含确切名称的列表。

**范例：筛选出“订单编号_A”和“客户ID_B”这两列**

Python

```
df_filtered_items = df.filter(items=['订单编号_A', '客户ID_B'])

print("\n使用 items 筛选列:")
print(df_filtered_items)
```

输出：

```
      订单编号_A  客户ID_B
行_1      101       1
行_2      102       2
行_3      103       3
```

你也可以用它来筛选行，只需指定 `axis=0`：

Python

```
df_filtered_rows = df.filter(items=['行_1', '行_3'], axis=0)

print("\n使用 items 筛选行:")
print(df_filtered_rows)
```

输出：

```
      订单编号_A  订单金额_A  客户ID_B  客户评级_B
行_1      101      340       1        5
行_3      103      120       3        5
```

#### 2. 使用 `like` 参数：模糊匹配（包含字符串）

`like` 用于筛选出名称中**包含**某个特定字符串的行或列。这在处理大量有规律命名的列时非常有用。

**范例：筛选出所有列名中包含 `_A` 的列**

Python

```
df_filtered_like = df.filter(like='_A') # 默认对列操作

print("\n使用 like 筛选列:")
print(df_filtered_like)
```

输出：

```
      订单编号_A  订单金额_A
行_1      101      340
行_2      102      500
行_3      103      120
```

#### 3. 使用 `regex` 参数：正则表达式匹配

`regex` 是最强大的参数，它允许你使用**正则表达式**来进行复杂的模式匹配。

**范例：筛选出所有以 `_B` 结尾的列** 正则表达式中，`$` 表示字符串的结尾。

Python

```
df_filtered_regex = df.filter(regex='_B$')

print("\n使用 regex 筛选列:")
print(df_filtered_regex)
```

输出：

```
      客户ID_B  客户评级_B
行_1       1        5
行_2       2        4
行_3       3        5
```

---

### 总结：何时使用 `filter`？

`filter` 方法特别适用于以下场景：

1. **动态筛选列**：当你的 DataFrame 有非常多的列，并且列名有一定规律时（例如，`sales_2023`, `sales_2024`, `profit_2023`, `profit_2024`），你可以轻松地用 `like` 或 `regex` 筛选出你想要的列组，例如 `df.filter(like='2024')`。
    
2. **数据清洗和准备**：在数据预处理阶段，可以方便地选取或排除某些特定模式命名的列。
    
3. **链式操作**：`filter` 可以很方便地嵌入到一长串的链式操作中，让代码保持简洁。
    

最后，再次强调它与另一种常见筛选方式的区别：

- `df.filter()`：根据**列名或行索引名**进行筛选（看“标签名”）。
    
- `df[df['订单金额_A'] > 200]`：根据**数据值**进行筛选（看“单元格的值”）。