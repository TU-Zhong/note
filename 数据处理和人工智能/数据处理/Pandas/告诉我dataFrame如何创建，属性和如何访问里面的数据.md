好的，我们来全面地了解一下 Pandas 中最核心的数据结构——`DataFrame`。

### 一、如何创建 DataFrame

创建 `DataFrame` 的方法非常灵活，最常见的几种方式如下：

#### 1. 从字典 (Dictionary) 创建

这是最常用、最直观的方法。你可以用一个字典来创建，其中**键（key）会成为列名（columns）**，**值（value）是每列的数据**。值的类型通常是列表、NumPy 数组或 Pandas Series。

Python

```
import pandas as pd

# 值是列表
data_dict = {
    '姓名': ['张三', '李四', '王五', '赵六'],
    '年龄': [25, 30, 22, 28],
    '城市': ['北京', '上海', '广州', '深圳']
}
df1 = pd.DataFrame(data_dict)
print(df1)
# 输出:
#    姓名  年龄  城市
# 0  张三  25  北京
# 1  李四  30  上海
# 2  王五  22  广州
# 3  赵六  28  深圳

# 你也可以在创建时指定行索引
df2 = pd.DataFrame(data_dict, index=['a', 'b', 'c', 'd'])
print(df2)
# 输出:
#    姓名  年龄  城市
# a  张三  25  北京
# b  李四  30  上海
# c  王五  22  广州
# d  赵六  28  深圳
```

#### 2. 从列表嵌套列表或 NumPy 二维数组创建

你可以用一个二维的列表或 NumPy 数组来创建，但需要手动指定列名和行索引。

Python

```
import numpy as np

# 从列表嵌套列表
data_list = [
    ['张三', 25, '北京'],
    ['李四', 30, '上海'],
    ['王五', 22, '广州']
]
df3 = pd.DataFrame(data_list, columns=['姓名', '年龄', '城市'])
print(df3)

# 从 NumPy 数组
data_np = np.array([
    [1.1, 2.2],
    [3.3, 4.4],
    [5.5, 6.6]
])
df4 = pd.DataFrame(data_np, columns=['A', 'B'], index=['row1', 'row2', 'row3'])
print(df4)
```

#### 3. 从字典嵌套字典创建

外层字典的键会成为**列名**，内层字典的键会成为**行索引**。

Python

```
data_nested_dict = {
    '北京': {'2020': 100, '2021': 110},
    '上海': {'2020': 120, '2021': 130}
}
df5 = pd.DataFrame(data_nested_dict)
print(df5)
# 输出:
#       北京   上海
# 2020  100  120
# 2021  110  130
```

#### 4. 从文件读取

这是实际工作中最常见的方式，Pandas 提供了强大的 I/O 工具。

Python

```
# 假设你有一个名为 'data.csv' 的文件
# df_from_csv = pd.read_csv('data.csv')

# 假设你有一个名为 'data.xlsx' 的 Excel 文件
# df_from_excel = pd.read_excel('data.xlsx')
```

---

### 二、DataFrame 的常用属性

属性提供了关于 `DataFrame` 的元数据信息，帮助你快速了解其结构。

假设我们使用上面创建的 `df1`：

- **`df1.index`**: 返回 `DataFrame` 的行索引。
    
    Python
    
    ```
    print(df1.index)  # 输出: RangeIndex(start=0, stop=4, step=1)
    ```
    
- **`df1.columns`**: 返回 `DataFrame` 的列名。
    
    Python
    
    ```
    print(df1.columns) # 输出: Index(['姓名', '年龄', '城市'], dtype='object')
    ```
    
- **`df1.values`**: 返回一个包含 `DataFrame` 所有数据的 NumPy 二维数组。
    
    Python
    
    ```
    print(df1.values)
    # 输出:
    # [['张三' 25 '北京']
    #  ['李四' 30 '上海']
    #  ['王五' 22 '广州']
    #  ['赵六' 28 '深圳']]
    ```
    
- **`df1.dtypes`**: 返回一个 `Series`，显示每一列的数据类型。
    
    Python
    
    ```
    print(df1.dtypes)
    # 输出:
    # 姓名    object
    # 年龄     int64
    # 城市    object
    # dtype: object
    ```
    
- **`df1.shape`**: 返回一个元组，表示 `DataFrame` 的维度（行数, 列数）。
    
    Python
    
    ```
    print(df1.shape)  # 输出: (4, 3)
    ```
    
- **`df1.size`**: 返回 `DataFrame` 中元素的总个数（行数 * 列数）。
    
    Python
    
    ```
    print(df1.size)  # 输出: 12
    ```
    
- **`df1.T`**: 转置 `DataFrame`，即行和列互换。
    
    Python
    
    ```
    print(df1.T)
    ```
    

---

### 三、如何访问 DataFrame 中的数据

访问数据是 `DataFrame` 最核心、最频繁的操作。推荐使用 `.loc` 和 `.iloc` 进行精确访问。

#### 1. 选择列 (Column Selection)

- **选择单列**: 返回一个 `Series`。
    
    Python
    
    ```
    # 语法糖，类似字典访问
    ages = df1['年龄']
    print(type(ages)) # <class 'pandas.core.series.Series'>
    
    # 属性访问（仅当列名不含空格或特殊字符时可用，不推荐）
    # ages = df1.年龄
    ```
    
- **选择多列**: 传入一个列名列表，返回一个新的 `DataFrame`。
    
    Python
    
    ```
    subset = df1[['姓名', '城市']]
    print(subset)
    ```
    

#### 2. 选择行 (Row Selection)

选择行必须使用 `.loc` 或 `.iloc`。

#### 3. 使用 `.loc` 和 `.iloc` 进行切片 (Slicing) - 推荐！

这是最标准、最不会引起混淆的访问方式。语法是 `df.loc[行标签, 列标签]` 和 `df.iloc[行位置, 列位置]`。

让我们使用 `df2` 进行演示，因为它有自定义的行索引。

Python

```
# df2:
#    姓名  年龄  城市
# a  张三  25  北京
# b  李四  30  上海
# c  王五  22  广州
# d  赵六  28  深圳
```

- **`.loc`：基于标签 (Label-based)**
    
    Python
    
    ```
    # 选择单行
    print(df2.loc['b'])
    
    # 选择多行 (使用标签列表)
    print(df2.loc[['a', 'd']])
    
    # 选择行和列
    print(df2.loc['c', '年龄'])  # 输出: 22
    
    # 选择多行和多列 (切片，包含结束标签)
    print(df2.loc['b':'d', ['姓名', '年龄']])
    ```
    
- **`.iloc`：基于整数位置 (Integer-location-based)**
    
    Python
    
    ```
    # 选择第一行 (位置 0)
    print(df2.iloc[0])
    
    # 选择第一行和第三行
    print(df2.iloc[[0, 2]])
    
    # 选择第2行、第1列的元素 (位置[1, 0])
    print(df2.iloc[1, 0])  # 输出: '李四'
    
    # 选择多行和多列 (切片，不包含结束位置)
    print(df2.iloc[0:2, 0:2]) # 选择第0、1行和第0、1列
    ```
    

#### 4. 条件筛选 (Boolean Indexing)

这是数据分析的精髓，用于根据条件过滤数据。

Python

```
# 筛选出年龄大于25的所有记录
young_people = df1[df1['年龄'] > 25]
print(young_people)

# 组合多个条件 (使用 & 表示“与”，| 表示“或”，每个条件用括号括起来)
# 筛选出年龄大于25且城市在北京的记录
filter_condition = (df1['年龄'] > 25) & (df1['城市'] == '北京')
print(df1[filter_condition]) # 在这个例子中没有符合条件的记录

# 筛选出城市是北京或上海的记录
filter_condition2 = (df1['城市'] == '北京') | (df1['城市'] == '上海')
print(df1[filter_condition2])
```

掌握了 `DataFrame` 的创建、属性和数据访问方法，你就已经具备了使用 Pandas 进行绝大多数数据处理任务的基础能力。