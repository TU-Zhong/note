这是一个经典的链表问题（LeetCode 160题）。

首先，我们需要定义链表的节点结构（这也是面试时的标准开头）：

Java

```
// 链表节点定义
public class ListNode {
    int val;
    ListNode next;
    ListNode(int x) {
        val = x;
        next = null;
    }
}
```

下面提供两种常见的解法：

---

### 解法一：哈希集合法 (Hash Set)

这是最直观的解法。我们利用哈希集合（Set）的特性：它可以存储不重复的元素，并且查找效率很高。

**思路：**

1. 遍历链表 A，将链表 A 中的所有节点（**注意是节点对象的引用/地址，不是值**）存入一个 `HashSet` 中。
    
2. 遍历链表 B，对于 B 中的每一个节点，检查它是否存在于 `HashSet` 中。
    
3. 第一个在 `HashSet` 中发现的节点，就是两个链表的相交起始节点。
    

**代码实现：**

Java

```
import java.util.HashSet;
import java.util.Set;

public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        Set<ListNode> visited = new HashSet<>();
        
        // 1. 遍历链表A，将所有节点加入集合
        ListNode temp = headA;
        while (temp != null) {
            visited.add(temp);
            temp = temp.next;
        }
        
        // 2. 遍历链表B，检查节点是否在集合中出现过
        temp = headB;
        while (temp != null) {
            if (visited.contains(temp)) {
                return temp; // 找到了相交点
            }
            temp = temp.next;
        }
        
        return null; // 没有相交
    }
}
```

- **时间复杂度：** $O(m + n)$，其中 $m$ 和 $n$ 分别是两个链表的长度。我们需要遍历两个链表各一次。
    
- **空间复杂度：** $O(m)$ 或 $O(n)$，需要额外的空间来存储其中一个链表的所有节点。
    

---

### 解法二：双指针法 (Two Pointers) —— 最优解

这个解法非常巧妙，通常被称为“走过对方走过的路”。它可以将空间复杂度降为 $O(1)$。

思路：

设链表 A 的非公共部分长度为 $a$，链表 B 的非公共部分长度为 $b$，公共部分（相交部分）长度为 $c$。

- 链表 A 的总长度：$a + c$
    
- 链表 B 的总长度：$b + c$
    

如果我们让两个指针 `pA` 和 `pB` 分别从 `headA` 和 `headB` 出发：

1. `pA` 走完链表 A 后，立刻跳到链表 B 的头部继续走。
    
2. `pB` 走完链表 B 后，立刻跳到链表 A 的头部继续走。
    

为什么会相遇？

当它们相遇时，它们走过的总路程是相等的：

- `pA` 走的路径：$a + c + b$
    
- pB 走的路径：$b + c + a$
    
    显然 $a + c + b = b + c + a$。它们会在公共部分的起点（也就是相交点）相遇。
    

如果不相交呢？

如果两个链表不相交，pA 和 pB 会同时走到 null（相当于走了 $m+n$ 的距离），此时循环结束，返回 null。

**代码实现：**

Java

```
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) {
            return null;
        }
        
        ListNode pA = headA;
        ListNode pB = headB;
        
        // 当 pA != pB 时循环，一旦相等（即相交点或都为null）则跳出
        while (pA != pB) {
            // pA 走完了吗？走完了就去走 headB，否则走 pA.next
            pA = (pA == null) ? headB : pA.next;
            
            // pB 走完了吗？走完了就去走 headA，否则走 pB.next
            pB = (pB == null) ? headA : pB.next;
        }
        
        // 这里的 pA 可能是相交节点，也可能是 null (如果不相交)
        return pA;
    }
}
```

- **时间复杂度：** $O(m + n)$，最坏情况下，两个指针都要遍历完两个链表。
    
- **空间复杂度：** $O(1)$，只使用了两个指针变量，不需要额外的数据结构。
    

---

### 总结

|**特性**|**哈希集合法**|**双指针法**|
|---|---|---|
|**理解难度**|容易|需要一点逻辑转换|
|**代码量**|中等|极简|
|**空间复杂度**|高 (需要存节点)|**极低 (最优)**|
|**面试推荐度**|保底方案|**首选方案**|

**建议：** 面试时优先写**解法二（双指针法）**，因为它展示了更好的算法思维和空间优化能力。

你需要我为你解释一下这道题的另一个变种“**环形链表**”怎么做吗？那也是双指针的经典应用。