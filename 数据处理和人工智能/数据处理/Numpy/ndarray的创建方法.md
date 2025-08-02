当然可以！

创建数组是使用 NumPy 的第一步。NumPy 提供了五花八门的方法，就像一个工具箱，让你能根据不同的需求，高效地“生产”出你想要的数组。

下面我们分类介绍几种最常用的创建 `ndarray` 的方法，并配上通俗解释和代码示例。

---

### 1. 从现有的 Python 数据创建

这是最基础、最直接的方法，就像是把 Python 的列表（list）或元组（tuple）“转化”成 NumPy 数组。

#### `np.array()`

- **通俗解释**：这是“手动”创建数组的方法。你给它一个 Python 列表，它帮你转换成一个高性能的 `ndarray`。
    
- **主要参数**：
    
    - `object`: 你的 Python 列表或元组。
        
    - `dtype` (可选): 指定数组的数据类型，如 `np.float64`。
        
- **代码示例**：
    
    Python
    
    ```Python
    import numpy as np
    
    # 从一个Python列表创建一维数组
    list1 = [1, 2, 3, 4]
    arr1 = np.array(list1)
    print("一维数组:\n", arr1)
    
    # 从一个嵌套列表创建二维数组
    list2 = [[1, 2, 3], [4, 5, 6]]
    arr2 = np.array(list2)
    print("二维数组:\n", arr2)
    
    # 创建时指定数据类型为浮点数
    arr3 = np.array([1, 2, 3], dtype=np.float64)
    print("指定类型的数组:\n", arr3)
    ```
    

---

### 2. 创建指定形状和值的“占位”数组

有时候我们事先不知道数组里要填什么具体数字，但知道需要一个多大规格的数组。这些方法就像是先预定一个“空的鸡蛋托盘”，然后再决定放什么。

#### `np.zeros()`

- **通俗解释**：创建一个所有元素都为 `0` 的数组。
    
- **代码示例**：
    
    Python
    
    ```Python
    # 创建一个2行3列，全为0的数组
    zeros_arr = np.zeros((2, 3))
    print(zeros_arr)
    # 输出:
    # [[0. 0. 0.]
    #  [0. 0. 0.]]
    ```
    

#### `np.ones()`

- **通俗解释**：创建一个所有元素都为 `1` 的数组。
    
- **代码示例**：
    
    Python
    
    ```Python
    # 创建一个3行2列，全为1的数组
    ones_arr = np.ones((3, 2))
    print(ones_arr)
    # 输出:
    # [[1. 1.]
    #  [1. 1.]
    #  [1. 1.]]
    ```
    

#### `np.full()`

- **通俗解释**：创建一个用你指定的任意数字来填充的数组。
    
- **代码示例**：
    
    Python
    
    ```Python
    # 创建一个2x2，所有元素都是数字7的数组
    full_arr = np.full((2, 2), 7)
    print(full_arr)
    # 输出:
    # [[7 7]
    #  [7 7]]
    ```
    

---

### 3. 创建序列数组

这些方法用于创建有规律的、像数列一样的数组。

#### `np.arange()`

- **通俗解释**：这是 NumPy 版本的 Python `range()` 函数，但功能更强大，可以设置浮点数步长。
    
- **主要参数**：`start` (起点, 可选), `stop` (终点, **不包含**), `step` (步长, 可选)。
    
- **代码示例**：
    
    Python
    
    ```Python
    # 创建一个从0到9（不含10）的数组
    arange_arr1 = np.arange(10)
    print(arange_arr1)  # 输出: [0 1 2 3 4 5 6 7 8 9]
    
    # 创建一个从0到9，步长为2的数组
    arange_arr2 = np.arange(0, 10, 2)
    print(arange_arr2)  # 输出: [0 2 4 6 8]
    ```
    

#### `np.linspace()`

- **通俗解释**：在指定的区间内，生成一个等间距的数组。你告诉它**起点、终点**和**想要多少个点**，它会自动计算步长。
    
- **主要参数**：`start` (起点), `stop` (终点, **默认包含**), `num` (元素数量)。
    
- **代码示例**：
    
    Python
    
    ```Python
    # 在0到10之间，均匀地取出5个点
    linspace_arr = np.linspace(0, 10, 5)
    print(linspace_arr)
    # 输出: [ 0.   2.5  5.   7.5 10. ]
    ```
    
    - **与 `arange` 的关键区别**：`arange` 关注**步长**，而 `linspace` 关注**点的数量**。
        

---

### 4. 创建随机数组

在模拟、统计和机器学习中，我们经常需要创建随机数组。

#### `np.random.rand()`

- **通俗解释**：创建一个指定形状的数组，其中填充了 `[0, 1)` 区间内均匀分布的随机**浮点数**。
    
- **代码示例**：
    
    Python
    
    ```Python
    # 创建一个2x3的随机浮点数数组
    rand_arr = np.random.rand(2, 3)
    print(rand_arr)
    ```
    

#### `np.random.randn()`

- **通俗解释**：创建一个指定形状的数组，其中填充了符合标准正态分布（“钟形曲线”）的随机**浮点数**。
    
- **代码示例**：
    
    Python
    
    ```Python
    # 创建一个2x3的标准正态分布数组
    randn_arr = np.random.randn(2, 3)
    print(randn_arr)
    ```
    

#### `np.random.randint()`

- **通俗解释**：创建一个指定形状的数组，其中填充了指定范围内的随机**整数**。
    
- **代码示例**：
    
    Python
    
    ```Python
    # 在1到100（不含100）之间，随机生成一个大小为(2,3)的整数数组
    randint_arr = np.random.randint(1, 100, size=(2, 3))
    print(randint_arr)
    ```
    

### 总结表格

|函数 (Function)|通俗解释|主要应用场景|
|---|---|---|
|**`np.array()`**|把 Python 列表变成 `ndarray`|从已有数据转换|
|**`np.zeros()`**|创建全 `0` 数组|初始化，清零|
|**`np.ones()`**|创建全 `1` 数组|初始化，作为掩码|
|**`np.full()`**|创建自定义数值的数组|创建特定值的数组|
|**`np.arange()`**|按“步长”生成序列|生成整数或等差序列|
|**`np.linspace()`**|按“数量”生成序列|科学计算、绘图坐标轴|
|**`np.random.rand()`**|`[0,1)` 均匀分布随机数|基础随机模拟|
|**`np.random.randn()`**|标准正态分布随机数|统计学、高级模拟|
|**`np.random.randint()`**|随机整数|模拟抽样、随机选择|

Export to Sheets

一开始可能会觉得方法很多，但最常用的就是 `np.array`, `np.arange`, `np.zeros` 和 `np.random` 系列。熟悉它们，您就能应对绝大多数场景了。