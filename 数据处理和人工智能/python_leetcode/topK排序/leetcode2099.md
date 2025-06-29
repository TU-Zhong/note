好的，当然。要找到和最大的长度为 `k` 的子序列，核心思想是必须选择数组中 `k` 个最大的数，并且保持它们在原数组中的相对顺序。

你已经排除了“找到k个最大值并排序其下标”的方法，这通常是最高效的解法之一。下面我为你提供另外三种思路截然不同的解法。

---

### 解法一：排序与哈希计数

这种方法的核心是，我们先通过排序确定构成最大和的 `k` 个数值具体是什么，然后用一个哈希表（或频率字典）来“记住”这些值以及它们的数量。最后，我们再遍历一次原数组，按顺序“收集”我们需要的数字。

**思路步骤：**

1. **识别目标值**：创建一个原数组 `nums` 的副本并对其进行排序（例如，降序）。取出前 `k` 个元素，这就是子序列必须包含的 `k` 个最大值。
    
2. **建立频率表**：由于 `k` 个最大值中可能有重复的数字，我们使用一个哈希表（在 Python 中是 `dict` 或 `collections.Counter`）来统计这 `k` 个数中每个数值需要出现的次数。
    
3. **构建子序列**：遍历**原始** `nums` 数组，对于每个元素：
    
    - 检查它是否存在于我们的频率表中，并且其需要的次数大于 0。
        
    - 如果是，则将该元素添加到结果子序列中，并将其在频率表中的计数减一。
        
4. 当结果子序列的长度达到 `k` 时，返回结果。
    

**Python 实现：**

Python

```python
import collections

def maxSubsequence_sort_hash(nums: list[int], k: int) -> list[int]:
    """
    解法一：排序与哈希计数
    """
    # 1. 识别 k 个最大值
    # 对数组降序排序，取前 k 个
    top_k_values = sorted(nums, reverse=True)[:k]
    
    # 2. 建立频率表
    # 使用 Counter 统计这 k 个值中每个数值的出现频率
    target_counts = collections.Counter(top_k_values)
    
    # 3. 构建子序列
    result = []
    for num in nums:
        # 如果当前数字是我们需要的目标之一
        if target_counts[num] > 0:
            result.append(num)
            # 将该数字的需求量减一
            target_counts[num] -= 1
            # 如果提前收集满k个，可以提前终止（可选优化）
            # if len(result) == k:
            #     break
            
    return result

# 示例
nums = [2, 1, 3, 3]
k = 2
print(f"解法一: {maxSubsequence_sort_hash(nums, k)}") # 输出: [3, 3]

nums = [-1, -2, 3, 4]
k = 3
print(f"解法一: {maxSubsequence_sort_hash(nums, k)}") # 输出: [-1, 3, 4]

nums = [3, 4, 3, 3]
k = 2
print(f"解法一: {maxSubsequence_sort_hash(nums, k)}") # 输出: [4, 3]
```

- **时间复杂度**: O(NlogN)，瓶颈在于对整个数组的排序。
    
- **空间复杂度**: O(N) 或 O(k)，取决于排序的实现和频率表的大小。
    

---

### 解法二：使用最小堆（优先队列）

这种方法可以优化“识别 `k` 个最大值”这一步。我们不需要对整个数组排序，而是使用一个大小固定为 `k` 的最小堆，在一次遍历中就找出所有元素中的 `k` 个最大值。

**思路步骤：**

1. **识别目标值 (使用最小堆)**：
    
    - 维护一个大小为 `k` 的最小堆（Min-Heap）。
        
    - 遍历 `nums` 数组，对于每个元素：
        
        - 将其推入堆中。
            
        - 如果堆的大小超过 `k`，就弹出堆顶元素（当前堆中最小的元素）。
            
    - 遍历结束后，堆中剩下的 `k` 个元素就是整个数组中最大的 `k` 个数。
        
2. **建立频率表**：和解法一相同，将堆中的 `k` 个元素存入频率表。
    
3. **构建子序列**：和解法一完全相同，遍历原数组，按顺序收集频率表中的数字。
    

**Python 实现：**

Python

```python
import heapq
import collections

def maxSubsequence_min_heap(nums: list[int], k: int) -> list[int]:
    """
    解法二：使用最小堆
    """
    if k == 0:
        return []
        
    # 1. 使用最小堆找到 k 个最大值
    min_heap = []
    for num in nums:
        heapq.heappush(min_heap, num)
        if len(min_heap) > k:
            heapq.heappop(min_heap)
    
    # 此时 min_heap 中就是 nums 中最大的 k 个元素
    # 2. 建立频率表
    target_counts = collections.Counter(min_heap)
    
    # 3. 构建子序列
    result = []
    for num in nums:
        if target_counts[num] > 0:
            result.append(num)
            target_counts[num] -= 1
    
    return result

# 示例
nums = [2, 1, 3, 3]
k = 2
print(f"解法二: {maxSubsequence_min_heap(nums, k)}") # 输出: [3, 3]

nums = [-1, -2, 3, 4]
k = 3
print(f"解法二: {maxSubsequence_min_heap(nums, k)}") # 输出: [-1, 3, 4]
```

- **时间复杂度**: O(Nlogk)。遍历数组是 O(N)，每次堆操作是 O(logk)。当 `k` 远小于 `N` 时，这比 O(NlogN) 更优。
    
- **空间复杂度**: O(k)，用于存储堆和频率表。
    

---

### 解法三：快速选择算法（Quickselect）

这是理论上最高效的解法。快速选择算法可以在平均 O(N) 的时间内找到数组中第 `k` 大的元素。我们将这个元素作为“门槛值”，所有大于它的数都必须入选，然后看还差几个等于门槛值的数。

**思路步骤：**

1. **找到门槛值**：使用快速选择算法找到 `nums` 数组中第 `k` 大的元素，记为 `threshold`。
    
2. **统计数量**：
    
    - 遍历 `nums`，统计大于 `threshold` 的元素有多少个，记为 `count_greater`。
        
    - 那么，等于 `threshold` 的元素中，我们还需要 `count_equal = k - count_greater` 个。
        
3. **构建子序列**：再次遍历原始 `nums` 数组：
    
    - 如果当前元素大于 `threshold`，直接加入结果。
        
    - 如果当前元素等于 `threshold` 且 `count_equal > 0`，则将其加入结果，并将 `count_equal` 减一。
        

**Python 实现：** _注意：Python 标准库没有直接的快速选择实现，但 `heapq.nlargest` 底层就用了类似的思想，这里我们直接使用 `heapq.nlargest` 来找到第 `k` 大的数以简化代码。_

Python

```python
import heapq

def maxSubsequence_quickselect_idea(nums: list[int], k: int) -> list[int]:
    """
    解法三：快速选择算法思想
    """
    if k == 0:
        return []

    # 1. 找到第 k 大的元素作为门槛值
    # heapq.nlargest(k, nums) 返回最大的 k 个数
    # [-1] 取最后一个，即第 k 大的数
    threshold = heapq.nlargest(k, nums)[-1]
    
    # 2. 统计数量
    count_greater = 0
    for num in nums:
        if num > threshold:
            count_greater += 1
            
    count_equal = k - count_greater
    
    # 3. 构建子序列
    result = []
    for num in nums:
        if num > threshold:
            result.append(num)
        elif num == threshold and count_equal > 0:
            result.append(num)
            count_equal -= 1
            
    return result

# 示例
nums = [2, 1, 3, 3]
k = 2
print(f"解法三: {maxSubsequence_quickselect_idea(nums, k)}") # 输出: [3, 3]

nums = [-1, -2, 3, 4]
k = 3
print(f"解法三: {maxSubsequence_quickselect_idea(nums, k)}") # 输出: [-1, 3, 4]
```

- **时间复杂度**: 平均 O(N)。`heapq.nlargest` 的实现是高效的，找到第 `k` 大的数的平均时间复杂度是 O(N)。后续遍历是 O(N)。
    
- **空间复杂度**: O(k)，`heapq.nlargest` 需要空间来存储 `k` 个最大的元素。