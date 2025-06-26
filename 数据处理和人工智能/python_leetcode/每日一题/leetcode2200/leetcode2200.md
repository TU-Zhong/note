好的，除了暴力解法（即对每一个下标 `i` 都去检查整个数组寻找符合条件的 `j`），这里提供三种更高效、思路不同的写法。

问题的核心在于，对于每一个 `key` 出现的位置 `j`，其影响范围是 `[j-k, j+k]`。所有最终的结果，就是所有 `key` 的影响范围的并集。

---

### 方法一：查找关键点，合并区间 (Find Keys, Merge Intervals)

这种方法最直观。我们先找到所有 `key` 的位置，然后对每个位置生成其影响的 `k` 近邻下标区间，最后将所有这些下标合并去重。使用集合 (`set`) 是处理合并去重最自然的方式。

**思路：**

1. 遍历一次 `nums` 数组，找到所有值为 `key` 的下标 `j`，存入一个列表 `key_indices`。
2. 创建一个空的集合 `result_set` 用于存放结果，集合可以自动处理重复的下标。
3. 遍历 `key_indices` 中的每一个下标 `j`。
4. 对于每个 `j`，都确定一个影响区间 `[j-k, j+k]`。将这个区间内所有有效的下标（即 `0` 到 `len(nums)-1` 范围内）都添加到 `result_set` 中。
5. 最后，将集合转换成列表并排序后返回。

**Python 代码：**

Python
[[数据处理和人工智能/python_leetcode/每日一题/leetcode2200/代码解释|代码解释]]
key_indices = [i for i, val in enumerate(nums) if val == key]
是什么写法
```python
def find_k_neighbors_merge(nums, key, k):
    """
    方法一：查找关键点，合并区间
    """
    n = len(nums)
    # 1. 找到所有 key 的位置
    key_indices = [i for i, val in enumerate(nums) if val == key]

    if not key_indices:
        return []

    result_set = set()
    
    # 2. 对每个 key 的位置，将其影响范围内的下标加入集合
    for j in key_indices:
        # 确定影响区间的左右边界
        # start = j - k if j - k >= 0 else 0 
        start = max(0, j - k)
        
        # end 是 range 的上界，所以要 +1，并且不能超过数组长度 n
        # end = j + k + 1 if j + k + 1 < n else n
        end = min(n, j + k + 1)
        
        for i in range(start, end):
            result_set.add(i)
            
    # 3. 集合转为排序列表
    return sorted(list(result_set))

# 示例
nums = [3,4,9,1,3,9,5]
key = 9
k = 1
print(f"方法一输出: {find_k_neighbors_merge(nums, key, k)}") # [1, 2, 3, 4, 5, 6]
```

**复杂度分析：**

- **时间复杂度:** O(N+Mcdotk)，其中 N 是 `nums` 的长度，M 是 `key` 出现的次数。遍历 `nums` 找 `key` 是 O(N)。之后对于 M 个 `key`，每个都循环 2k+1 次。
- **空间复杂度:** O(M+R)，其中 M 是存储 `key` 位置所需的空间，R 是结果集合的大小。最坏情况下 R 可以达到 O(N)。

---

### 方法二：两次遍历法 (Two-Pass Scan)

这是最高效的方法。其核心思想是，对于任何一个下标 `i`，它是否为 K 近邻下标，取决于它离左边最近的 `key` 的距离，和离右边最近的 `key` 的距离，只要这两个距离中有一个小于等于 `k` 即可。

**思路：**

1. **第一次遍历 (从左到右):**
    - 创建一个 `distances` 数组，初始化所有值为无穷大。
    - 维护一个变量 `lastKeyPos` 记录上一个遇到的 `key` 的位置，初始为负无穷大。
    - 从左到右遍历 `nums`，如果 `nums[i] == key`，更新 `lastKeyPos = i`。
    - 在每一步，计算当前下标 `i` 与 `lastKeyPos` 的距离，并更新 `distances[i] = i - lastKeyPos`。此时 `distances[i]` 记录了 `i` 到其左侧最近 `key` 的距离。
2. **第二次遍历 (从右到左):**
    - 重置 `lastKeyPos` 为正无穷大。
    - 从右到左遍历 `nums`，如果 `nums[i] == key`，更新 `lastKeyPos = i`。
    - 在每一步，计算当前下标 `i` 与 `lastKeyPos` 的距离 `lastKeyPos - i`，并用这个值去更新 `distances[i]`。具体来说，`distances[i] = min(distances[i], lastKeyPos - i)`。
    - 这次遍历结束后，`distances[i]` 就存储了 `i` 到左右两侧 `key` 的最短距离。
3. **第三次遍历 (构建结果):**
    - 遍历 `distances` 数组，将所有 `distances[i] <= k` 的下标 `i` 加入结果列表。

**Python 代码：**

Python

```python
import math

def find_k_neighbors_two_pass(nums, key, k):
    """
    方法二：两次遍历法
    """
    n = len(nums)
    result = []
    
    # 检查是否有 key 存在，没有则直接返回
    if key not in nums:
        return []
        
    distances = [math.inf] * n
    
    # 1. 从左到右遍历，计算到左边 key 的距离
    last_key_pos = -math.inf
    for i in range(n):
        if nums[i] == key:
            last_key_pos = i
        distances[i] = i - last_key_pos

    # 2. 从右到左遍历，计算到右边 key 的距离，并取两者最小值
    last_key_pos = math.inf
    for i in range(n - 1, -1, -1):
        if nums[i] == key:
            last_key_pos = i
        distances[i] = min(distances[i], last_key_pos - i)
        
    # 3. 收集所有距离小于等于 k 的下标
    for i in range(n):
        if distances[i] <= k:
            result.append(i)
            
    return result

# 示例
nums = [3,4,9,1,3,9,5]
key = 9
k = 1
print(f"方法二输出: {find_k_neighbors_two_pass(nums, key, k)}") # [1, 2, 3, 4, 5, 6]
```

**复杂度分析：**

- **时间复杂度:** O(N)。因为我们只对数组进行了三次独立的线性扫描。这是最优的时间复杂度。
- **空间复杂度:** O(N)。需要一个额外的 `distances` 数组来存储距离。

---

### 方法三：布尔标记法 (Boolean Flagging)

这种方法与方法一类似，但是不使用集合，而是用一个布尔数组来标记哪些下标是 K 近邻下标。这可以避免集合操作的开销，在某些情况下可能更快。

**思路：**

1. 创建一个与 `nums` 等长的布尔数组 `is_k_neighbor`，全部初始化为 `False`。
2. 遍历 `nums` 数组，一旦找到 `nums[j] == key`：
    - 我们知道从 `j-k` 到 `j+k` 的所有下标都满足条件。
    - 立即在这个影响范围内，将 `is_k_neighbor` 数组对应位置的元素标记为 `True`。要注意数组边界。
3. 标记完成后，遍历 `is_k_neighbor` 数组，将所有值为 `True` 的下标收集起来，形成最终结果。

**Python 代码：**

Python

```python
def find_k_neighbors_flagging(nums, key, k):
    """
    方法三：布尔标记法
    """
    n = len(nums)
    is_k_neighbor = [False] * n
    
    for j in range(n):
        if nums[j] == key:
            # 标记从 j-k 到 j+k 的范围
            start = max(0, j - k)
            end = min(n, j + k + 1)
            for i in range(start, end):
                is_k_neighbor[i] = True
    
    # 从标记数组中生成结果列表
    # 使用列表推导式可以更简洁
    return [i for i, is_neighbor in enumerate(is_k_neighbor) if is_neighbor]

# 示例
nums = [3,4,9,1,3,9,5]
key = 9
k = 1
print(f"方法三输出: {find_k_neighbors_flagging(nums, key, k)}") # [1, 2, 3, 4, 5, 6]
```

**复杂度分析：**

- **时间复杂度:** O(Ncdotk)。在最坏情况下，如果 `nums` 中所有元素都是 `key`，那么对于每个元素，我们都要向两边扩展 `k` 步。如果 `k` 很大，性能会下降。
- **空间复杂度:** O(N)。需要一个额外的布尔数组。

### 总结对比

|方法|时间复杂度|空间复杂度|优点|缺点|
|:--|:--|:--|:--|:--|
|**方法一：合并区间**|O(N+Mcdotk)|O(N)|思路直观，容易理解|当 `key` 很多时，性能不如方法二|
|**方法二：两次遍历**|O(N)|O(N)|**时间效率最高**，不受 `k` 和 `key` 数量影响|思路稍显复杂，需要两次遍历|
|**方法三：布尔标记**|O(Ncdotk)|O(N)|实现简单，不需排序和集合操作|当 `k` 很大或 `key` 很多时，效率较低|

Export to Sheets

对于绝大多数情况，**方法二（两次遍历法）** 是最佳选择。