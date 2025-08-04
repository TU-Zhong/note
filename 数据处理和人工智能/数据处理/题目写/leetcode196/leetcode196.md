好的，这个问题要求我们**直接修改（in-place）**给定的 `DataFrame`，删除重复的 email，并只保留每个 email 中 `id` 最小的那一行。

这是一个非常经典的数据清洗场景。在 Pandas 中，解决这个问题的最佳方法是巧妙地结合 `sort_values()` 和 `drop_duplicates()`。

### 解决方案

Python

```python
import pandas as pd

def delete_duplicate_emails(person: pd.DataFrame) -> None:
    """
    删除重复的电子邮件，只保留一个具有最小 id 的唯一电子邮件。
    此函数直接修改输入的 DataFrame (in-place)，不返回任何内容。

    Args:
        person: 一个包含 'id' 和 'email' 列的 DataFrame。
    """
    # 步骤 1: 根据 id 对整个 DataFrame 进行升序排序。
    # 这确保了对于任何重复的 email，id 最小的那一行总是排在最前面。
    # inplace=True 表示直接在原 DataFrame 上进行修改。
    person.sort_values(by='id', inplace=True)
    
    # 步骤 2: 根据 'email' 列删除重复项，并保留第一个出现的记录。
    # 因为上一步已经按 id 排序，所以“第一个出现的记录”就一定是 id 最小的那个。
    # inplace=True 表示直接删除重复行。
    person.drop_duplicates(subset=['email'], keep='first', inplace=True)
```

### 代码解析

这套“**先排序，后去重**”的组合拳是解决此类问题的黄金法则。

1. **`person.sort_values(by='id', inplace=True)`**
    
    - **`sort_values(...)`**: 这是 Pandas 用于排序的方法。
        
    - **`by='id'`**: 我们告诉 Pandas 按照 `id` 这一列的值来进行排序。默认是升序（从低到高）。
        
    - **`inplace=True`**: 这是本题的一个关键点。它表示**直接在原始的 `person` DataFrame 上进行排序操作**，而不是创建一个排好序的新 DataFrame。执行后，`person` 自身就被改变了。
        
    
    **执行此步后的效果（以示例数据为例）**： 假设原始数据顺序是乱的（比如 id=3 在 id=1 前面），经过这一步，它一定会变成：
    
    ```
       id             email
    0   1  john@example.com
    1   2   bob@example.com
    2   3  john@example.com
    ```
    
    现在，对于 `john@example.com` 这个 email，`id` 为 1 的行保证排在了 `id` 为 3 的行的前面。
    
2. **`person.drop_duplicates(subset=['email'], keep='first', inplace=True)`**
    
    - **`drop_duplicates(...)`**: 这是 Pandas 用于删除重复行的方法。
        
    - **`subset=['email']`**: 这个参数至关重要。它告诉 Pandas 判断重复的依据**仅仅是 `email` 这一列**。如果两个行的 `email` 值相同，它们就被视为重复，而不管 `id` 是否相同。
        
    - **`keep='first'`**: 当发现重复项时，这个参数决定保留哪一个。`'first'` 表示**保留第一次出现的记录**，删除后面的所有重复项。结合上一步的排序，这里的“第一次出现”就等同于“`id` 最小的那个”。
        
    - **`inplace=True`**: 同样，这个参数表示**直接从 `person` DataFrame 中删除那些被判为重复的行**。
        
    
    **执行此步后的效果**： `drop_duplicates` 开始扫描已经排好序的 DataFrame：
    
    1. 看到 `id=1, email='john@example.com'`。这是 `'john@example.com'` 第一次出现，根据 `keep='first'`，保留它。
        
    2. 看到 `id=2, email='bob@example.com'`。这是 `'bob@example.com'` 第一次出现，保留它。
        
    3. 看到 `id=3, email='john@example.com'`。`'john@example.com'` 之前已经出现过了，所以这一行是重复项，**删除它**。
        
    
    最终，`person` DataFrame 就地被修改成了题目要求的结果。
    
3. **函数返回 `None`**
    
    - 因为所有的修改都是通过 `inplace=True` 直接在传入的 `person` 对象上完成的，所以函数本身不需要返回任何东西。Python 函数如果没有显式的 `return` 语句，会默认返回 `None`，这正好符合函数签名 `-> None` 的要求。