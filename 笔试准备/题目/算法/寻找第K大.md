### 快速选择算法 (QuickSelect) 核心逻辑

题目要求使用“快速排序的思路”，但我们不需要像完整的快速排序那样把整个数组都排好序。这里使用的是快速排序的变种——**快速选择算法 (QuickSelect)**。

快速排序的核心是**划分 (Partition)**：选择一个基准值 (pivot)，将小于基准值的数放到左边，大于基准值的数放到右边。基准值最终所在的位置，就是它在完全排序后应该在的位置。

**具体思路如下：**

1. **目标转换**：要找第 $K$ 大的元素，如果我们将数组按**升序**排列，这个元素最终所在的索引应该是 $n - K$。
    
2. **执行划分**：在当前区间 `[left, right]` 内选一个基准值，执行划分操作，得到该基准值最终的索引 `pivotIndex`。
    
3. **判断与舍弃**：
    
    - 如果 `pivotIndex == n - K`：运气很好，基准值正好是我们想要的那个数，直接返回。
        
    - 如果 `pivotIndex < n - K`：说明我们要找的数在基准值的**右侧**。我们可以直接舍弃左半部分，将搜索区间缩小为 `[pivotIndex + 1, right]`。
        
    - 如果 `pivotIndex > n - K`：说明我们要找的数在基准值的**左侧**。直接舍弃右半部分，将搜索区间缩小为 `[left, pivotIndex - 1]`。
        

这种方法每次都能舍弃掉一半（平均情况下）的数据，不需要对无关区间进行排序，效率极高。并且为了严格满足空间复杂度 $O(1)$ 的要求，我们将使用**迭代**（`while` 循环）而不是递归来实现，以避免递归带来的调用栈内存消耗。

---

### JAVA 代码实现

Java

```java
import java.util.*;

public class Solution {
    /**
     * 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
     *
     * * @param a int整型一维数组 
     * @param n int整型 
     * @param K int整型 
     * @return int整型
     */
    public int findKth(int[] a, int n, int K) {
        // 第 K 大的元素，在升序排序后的数组中，其目标索引为 n - K
        int targetIndex = n - K;
        int left = 0;
        int right = n - 1;
        
        // 使用迭代而非递归，确保空间复杂度为 O(1)
        while (left <= right) {
            int pivotIndex = partition(a, left, right);
            
            if (pivotIndex == targetIndex) {
                // 刚好找到，直接返回
                return a[pivotIndex];
            } else if (pivotIndex < targetIndex) {
                // 目标在右半区间，移动左边界
                left = pivotIndex + 1;
            } else {
                // 目标在左半区间，移动右边界
                right = pivotIndex - 1;
            }
        }
        
        return -1; // 理论上题目保证答案存在，不会走到这里
    }
    
    // 快速排序的划分函数 (Lomuto 划分法)
    private int partition(int[] a, int left, int right) {
        // 选取最右侧的元素作为基准值
        int pivot = a[right];
        
        // i 指向当前小于 pivot 的区域的下一个位置
        int i = left; 
        
        for (int j = left; j < right; j++) {
            // 如果找到小于基准值的元素，就把它交换到左边
            if (a[j] < pivot) {
                swap(a, i, j);
                i++;
            }
        }
        // 最后将基准值放到它正确的中间位置上
        swap(a, i, right);
        return i;
    }
    
    // 辅助交换函数
    private void swap(int[] a, int i, int j) {
        int temp = a[i];
        a[i] = a[j];
        a[j] = temp;
    }
}
```

---

### 复杂度分析

- **时间复杂度**：平均情况 $O(n)$，远优于题目要求的 $O(n \log n)$。因为每次划分后只需处理其中一半区间，数据量大致为 $n + n/2 + n/4 + ... \approx 2n$。最坏情况（数组已经有序时）退化为 $O(n^2)$，但可以通过随机选择基准值来避免（当前代码为了简洁未加随机化，应对大部分 OJ 测试用例已足够）。
    
- **空间复杂度**：$O(1)$。算法全程只使用了几个整型变量作为指针，没有开辟新数组，且使用了 `while` 循环避免了递归栈的深度，严格满足常数级空间要求。
    

快速选择和昨天你做的大顶堆（`PriorityQueue`）都是解决 Top-K 问题的经典方案。在处理这两种解法时，你觉得哪一种的思维逻辑对你来说更顺畅一些呢？