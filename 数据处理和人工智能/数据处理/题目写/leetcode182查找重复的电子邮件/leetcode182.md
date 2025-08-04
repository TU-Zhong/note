好的，这个问题是数据处理中一个非常常见的任务：查找重复项。在 Pandas 中，我们有多种高效的方法可以解决它。

### 解决方案

这里提供一个非常简洁且高效的方案，使用 `value_counts()` 方法。
[[数据处理和人工智能/数据处理/题目写/leetcode182查找重复的电子邮件/写法1/解释1|解释1]]
[[数据处理和人工智能/数据处理/题目写/leetcode182查找重复的电子邮件/写法1/解释2|解释2]]
[[数据处理和人工智能/数据处理/题目写/leetcode182查找重复的电子邮件/写法1/代码解释3|代码解释3]]
Python

```python
import pandas as pd

def duplicate_emails(person: pd.DataFrame) -> pd.DataFrame:
    """
    报告所有重复的电子邮件。

    Args:
        person: 一个包含 'id' 和 'email' 列的 DataFrame。

    Returns:
        一个包含重复电子邮件的 DataFrame，列名为 'Email'。
    """
    # 1. 使用 value_counts() 计算每个 email 出现的次数
    email_counts = person['email'].value_counts()
    
    # 2. 筛选出出现次数大于 1 的 email
    #    这些 email 的名称在结果 Series 的索引 (index) 中
    duplicate_emails_index = email_counts[email_counts > 1].index
    
    # 3. 将结果转换为要求的 DataFrame 格式
    return pd.DataFrame({'Email': duplicate_emails_index})

```

### 代码解析

1. **核心思想** 解决问题的关键在于先统计出每种电子邮件地址（`email`）在数据中出现了多少次，然后筛选出那些出现次数超过一次的即可。
    
2. **`person['email'].value_counts()`**
    
    - `person['email']`：首先，我们从 `person` 这个 `DataFrame` 中选择出 `email` 这一列，得到一个 Pandas `Series`。
        
    - `.value_counts()`：这是 `Series` 对象的一个非常实用的方法，它会快速计算出 `Series` 中每个唯一值出现的频率（次数），并返回一个新的 `Series`。这个新 `Series` 的**索引（index）是原始数据中的唯一值（即不同的 email 地址）**，而它的**值（value）是该 email 出现的次数**。
        
    - 对于示例数据，`email_counts` 的结果会是：
        
        ```
        email
        a@b.com    2
        c@d.com    1
        Name: count, dtype: int64
        ```
        
3. **`email_counts[email_counts > 1].index`**
    
    - `email_counts > 1`：我们对上一步得到的计数值进行判断，这将产生一个布尔 `Series` (`True` 或 `False`)。
        
        ```
        email
        a@b.com     True
        c@d.com    False
        Name: count, dtype: bool
        ```
        
    - `email_counts[...]`：我们用这个布尔 `Series` 作为索引，来筛选出值为 `True` 的那些行。这被称为**布尔索引**。结果只剩下 `a@b.com` 那一行。
        
    - `.index`：我们从筛选后的结果中，只取其**索引**，因为索引中包含了我们真正需要的 email 地址。此时，`duplicate_emails_index` 就是 `Index(['a@b.com'], dtype='object', name='email')`。
        
4. **`pd.DataFrame({'Email': duplicate_emails_index})`**
    
    - 最后，我们使用这个包含重复 email 的索引来创建一个新的 `DataFrame`。
        
    - `{'Email': ...}`：我们创建一个字典，键 `'Email'` 将成为新 `DataFrame` 的列名，值就是我们找到的重复 email 列表。这样就得到了符合题目要求的最终输出格式。
        

### 其他可行的方法

Pandas 的强大之处在于解决同一个问题通常有多种路径。

#### 方法二：使用 `groupby()`

这是更通用的方法，思路与 `value_counts()` 类似，但步骤更分解。

Python

```python
def duplicate_emails_groupby(person: pd.DataFrame) -> pd.DataFrame:
    # 按 'email' 分组，并计算每个组的大小（即出现次数）
    email_counts = person.groupby('email').size()
    
    # 筛选出组大小 > 1 的 email
    duplicate_emails = email_counts[email_counts > 1].index
    
    # 构建最终的 DataFrame
    return pd.DataFrame({'Email': duplicate_emails})
```

#### 方法三：使用 `duplicated()`

这个方法更侧重于直接标记重复的行。

Python

```python
def duplicate_emails_duplicated(person: pd.DataFrame) -> pd.DataFrame:
    # 找到所有属于重复项的行 (keep=False 会标记所有重复出现的项，而不仅仅是第二个之后)
    is_duplicate = person['email'].duplicated(keep=False)
    
    # 筛选出这些重复的 email
    duplicate_emails = person[is_duplicate]['email']
    
    # 因为上面可能会包含同一个 email 多次，所以取唯一值
    unique_duplicate_emails = duplicate_emails.unique()
    
    return pd.DataFrame({'Email': unique_duplicate_emails})
```

对于这个问题，第一种使用 `value_counts()` 的方法最为简洁明了，是首选方案。