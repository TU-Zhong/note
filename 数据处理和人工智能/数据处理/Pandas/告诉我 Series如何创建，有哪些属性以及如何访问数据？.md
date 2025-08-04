好的，我们来详细讲解 Pandas 中 `Series` 的创建、常用属性以及数据访问方法。

### 一、如何创建 Series

创建 `Series` 的基本语法是 `pandas.Series(data, index, dtype, name)`。其中 `data` 是必需的，其他是可选参数。

#### 1. 从列表 (List) 创建

这是最常见的方式。如果你不指定 `index`，Pandas 会自动创建一个从 0 开始的整数索引。

Python

```
import pandas as pd

# 不指定索引
s1 = pd.Series([10, 20, 30, 40])
print(s1)
# 输出:
# 0    10
# 1    20
# 2    30
# 3    40
# dtype: int64

# 指定索引
s2 = pd.Series([10, 20, 30, 40], index=['a', 'b', 'c', 'd'], name='MySeries')
print(s2)
# 输出:
# a    10
# b    20
# c    30
# d    40
# Name: MySeries, dtype: int64
```

#### 2. 从 NumPy 数组 (ndarray) 创建

用法和从列表创建非常相似。

Python

```
import numpy as np

arr = np.array([1.1, 2.2, 3.3])
s3 = pd.Series(arr, index=['x', 'y', 'z'])
print(s3)
# 输出:
# x    1.1
# y    2.2
# z    3.3
# dtype: float64
```

#### 3. 从字典 (Dictionary) 创建

如果用字典创建，字典的键（key）会自动成为 `Series` 的索引，字典的值（value）成为 `Series` 的数据。

Python

```
data_dict = {'北京': 100, '上海': 200, '广州': 150}
s4 = pd.Series(data_dict)
print(s4)
# 输出:
# 北京    100
# 上海    200
# 广州    150
# dtype: int64

# 如果额外指定了 index，Pandas会根据新的 index 进行匹配
# 匹配不上的值为 NaN (Not a Number)
s5 = pd.Series(data_dict, index=['上海', '北京', '深圳'])
print(s5)
# 输出:
# 上海    200.0
# 北京    100.0
# 深圳      NaN
# dtype: float64
```

#### 4. 从标量值 (Scalar) 创建

如果 `data` 是一个单独的值（标量），那么必须提供 `index`。这个值会被重复填充到每个索引位置。

Python

```
s6 = pd.Series(5, index=['a', 'b', 'c'])
print(s6)
# 输出:
# a    5
# b    5
# c    5
# dtype: int64
```

---

### 二、Series 的常用属性

属性提供了关于 `Series` 的元信息，通过 `series对象.属性名` 的方式访问。

假设我们有以下 `Series`：

Python

```
s = pd.Series([10, 20, 30], index=['a', 'b', 'c'], name='demo')
```

- **`s.values`**: 返回一个包含 `Series` 中所有数据值的 NumPy 数组。
    
    Python
    
    ```
    print(s.values)  # 输出: [10 20 30]
    ```
    
- **`s.index`**: 返回 `Series` 的索引对象。
    
    Python
    
    ```
    print(s.index)  # 输出: Index(['a', 'b', 'c'], dtype='object')
    ```
    
- **`s.dtype`**: 返回 `Series` 中数据值的类型。
    
    Python
    
    ```
    print(s.dtype)  # 输出: int64
    ```
    
- **`s.name`**: 返回 `Series` 的名称。你可以给它赋值来修改名称。
    
    Python
    
    ```
    print(s.name)  # 输出: 'demo'
    s.name = 'new_demo'
    print(s.name)  # 输出: 'new_demo'
    ```
    
- **`s.size`**: 返回 `Series` 中元素的个数。
    
    Python
    
    ```
    print(s.size)  # 输出: 3
    ```
    
- **`s.shape`**: 返回一个表示 `Series` 维度的元组。对于 `Series`，它总是一个一维的元组 `(n,)`。
    
    Python
    
    ```
    print(s.shape)  # 输出: (3,)
    ```
    
- **`s.hasnans`**: 返回一个布尔值，表示 `Series` 中是否包含缺失值（NaN）。
    
    Python
    
    ```
    print(s.hasnans)  # 输出: False
    ```
    

---

### 三、如何访问 Series 中的数据

访问数据是 `Series` 最核心的操作之一。主要有以下几种方式：

假设我们有以下 `Series` 用于演示：

Python

```
s = pd.Series([10, 20, 30, 40, 50], index=['a', 'b', 'c', 'd', 'e'])
```

#### 1. 通过索引标签 (Label-based Indexing)

这是最直观的方式，类似于字典的键值访问。

- **访问单个元素**:
    
    Python
    
    ```
    print(s['b'])  # 输出: 20
    ```
    
- **访问多个元素 (切片)**:
    
    Python
    
    ```
    # 使用标签列表
    print(s[['a', 'c', 'e']])
    # 输出:
    # a    10
    # c    30
    # e    50
    # dtype: int64
    
    # 使用标签切片（注意：包含结束标签）
    print(s['b':'d'])
    # 输出:
    # b    20
    # c    30
    # d    40
    # dtype: int64
    ```
    

#### 2. 通过位置整数 (Position-based Indexing)

类似于列表的下标访问。

- **访问单个元素**:
    
    Python
    
    ```
    print(s[1])  # 输出: 20
    ```
    
- **访问多个元素 (切片)**:
    
    Python
    
    ```
    # 使用整数列表
    print(s[[0, 2, 4]])
    # 输出:
    # a    10
    # c    30
    # e    50
    # dtype: int64
    
    # 使用整数切片（注意：不包含结束位置，和Python列表切片规则一样）
    print(s[1:4])
    # 输出:
    # b    20
    # c    30
    # d    40
    # dtype: int64
    ```
    

> **注意**：直接使用 `[]` 进行索引有时会产生歧义。例如，如果索引本身就是整数 `s = pd.Series([10,20], index=[1,2])`，那么 `s[1]` 是按标签 `1` 访问还是按位置 `1` 访问？为了避免这种混淆，Pandas 提供了更明确的索引器：`.loc` 和 `.iloc`。

#### 3. 使用 `.loc` 和 `.iloc` 索引器 (推荐)

这是最推荐的、最清晰的访问方式。

- **`.loc` (Location-based)**: **专门用于标签索引**。
    
    Python
    
    ```
    print(s.loc['b'])         # 单个标签
    print(s.loc[['a', 'd']]) # 多个标签
    print(s.loc['b':'d'])      # 标签切片 (包含结束)
    ```
    
- **`.iloc` (Integer-location-based)**: **专门用于整数位置索引**。
    
    Python
    
    ```
    print(s.iloc[1])       # 单个位置
    print(s.iloc[[0, 3]]) # 多个位置
    print(s.iloc[1:4])     # 位置切片 (不包含结束)
    ```
    

#### 4. 条件筛选 (Boolean Indexing)

这是数据分析中非常强大的功能，可以根据条件过滤数据。

Python

```
# 筛选出值大于30的元素
print(s > 30)
# 输出:
# a    False
# b    False
# c    False
# d     True
# e     True
# dtype: bool

# 将上面的布尔Series作为索引传入
print(s[s > 30])
# 输出:
# d    40
# e    50
# dtype: int64
```

掌握了以上内容，你就对 `Series` 的基本操作有了扎实的了解，可以开始用它来处理一维数据了。