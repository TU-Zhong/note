这是一个非常经典的二叉树题目（LeetCode 236题）。

首先，给出二叉树节点的标准定义：

Java

```
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}
```

这道题主要考察对**递归**（DFS）的理解，以及如何利用**哈希表**记录父节点信息。

---

### 解法一：递归法 (DFS / 后序遍历) —— 最优解

这是最常用的解法，代码非常简洁，但逻辑需要一点时间消化。它的核心思想是**自底向上**（Bottom-up）的反馈。

核心思路：

想象你在遍历这棵树，你的任务是向你的父节点“汇报”情况：

1. **终止条件：** 如果当前节点为空，或者当前节点就是 `p` 或 `q`，那么直接返回当前节点（表示找到了）。
    
2. **递归搜索：**
    
    - 在左子树找 `p` 或 `q`。
        
    - 在右子树找 `p` 或 `q`。
        
3. **判断逻辑（关键）：**
    
    - **情况 A：** 如果左边找到了（不为空），右边也找到了（不为空），说明 `p` 和 `q` 分别在当前节点的两侧。那么**当前节点**就是最近公共祖先。
        
    - **情况 B：** 如果只有左边找到了，说明 `p` 和 `q` 都在左子树里（或者其中一个是另一个的祖先），直接把左边的结果往上抛。
        
    - **情况 C：** 同理，如果只有右边找到了，直接把右边的结果往上抛。
        

**代码实现：**


```java
TreeNode left = lowestCommonAncestor(root.left, p, q);

TreeNode right = lowestCommonAncestor(root.right, p, q);
```
用最通俗的大白话来说，这两行代码的意思是：**作为当前的根节点（root），我派了两个“侦察兵”，分别去我的左边领地（左子树）和右边领地（右子树）搜索，看看能不能找到目标人物 `p` 或者 `q`。**


```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // 1. 递归终止条件
        // 如果越过叶子节点，返回 null
        // 如果当前节点是 p 或 q，直接返回当前节点（告诉父节点“我找到了其中一个”）
        if (root == null || root == p || root == q) {
            return root;
        }

        // 2. 递（向下搜索）
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        //

        // 3. 归（向上回溯时的判断）
        
        // 如果左子树和右子树都找到了节点（都不为空）
        // 说明 p 和 q 分别在当前节点的两侧，当前节点就是 LCA
        if (left != null && right != null) {
            return root;
        }

        // 如果只有一边找到了，说明 p 和 q 都在那一边
        // 返回非空的那一个（继续向上汇报）
        return (left != null) ? left : right;
    }
}
```

- **时间复杂度：** $O(N)$，最坏情况需要遍历所有节点。
    
- **空间复杂度：** $O(N)$，最坏情况退化为链表，递归栈深度为 $N$。
    

---

### 解法二：哈希表存储父节点法 (Map + Set)

这个解法更符合直觉，特别是如果你刚做过**“两个链表相交”**那道题，你会发现这个解法本质上就是把树的问题转化为了链表相交问题。

核心思路：

二叉树只有指向子节点的指针，没有指向父节点的指针。如果我们能知道每个节点的父节点，这道题就变成了：

- 从 `p` 往上走，是一条链表。
    
- 从 `q` 往上走，是另一条链表。
    
- **找这两个链表的第一个交点。**
    

**步骤：**

1. **记录父节点：** 遍历整棵树（DFS或BFS），用一个 `HashMap` 记录所有节点的父节点（Key=当前节点, Value=父节点）。
    
2. **构建 p 的祖先路径：** 从 `p` 开始，不断往上找父节点，把路径上所有节点存入一个 `HashSet`。
    
3. **查找最近公共祖先：** 从 `q` 开始往上找父节点，检查遇到的第一个出现在 `HashSet` 中的节点，那就是 LCA。
    

**代码实现：**

Java

```java
import java.util.*;

class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // 记录父节点的 Map: key = 节点, value = 父节点
        Map<TreeNode, TreeNode> parentMap = new HashMap<>();
        // 辅助遍历的栈
        Deque<TreeNode> stack = new ArrayDeque<>();
        
        // 初始化
        parentMap.put(root, null); // 根节点没有父节点
        stack.push(root);

        // 1. 遍历树，直到找到 p 和 q 的父节点信息都已记录为止
        while (!parentMap.containsKey(p) || !parentMap.containsKey(q)) {
            TreeNode node = stack.pop();
            
            if (node.left != null) {
                parentMap.put(node.left, node);
                stack.push(node.left);
            }
            if (node.right != null) {
                parentMap.put(node.right, node);
                stack.push(node.right);
            }
        }

        // 2. 把 p 的所有祖先节点（包括 p 自己）加入 Set
        Set<TreeNode> ancestors = new HashSet<>();
        while (p != null) {
            ancestors.add(p);
            p = parentMap.get(p); // 向上移动
        }

        // 3. 从 q 开始向上走，第一个出现在 ancestors 里的就是 LCA
        while (!ancestors.contains(q)) {
            q = parentMap.get(q); // 向上移动
        }

        return q;
    }
}
```

- **时间复杂度：** $O(N)$，遍历树保存父节点需要 $O(N)$，查找过程最坏也是 $O(N)$。
    
- **空间复杂度：** $O(N)$，需要 Map 存储父节点指针和 Set 存储路径。
    

---

### 总结与对比

|**特性**|**递归法 (DFS)**|**哈希表法 (Map)**|
|---|---|---|
|**思维模式**|**分治/回溯** (自底向上汇报)|**路径回溯** (转化为链表相交)|
|**代码量**|极少 (几行)|较多 (需要维护 Map)|
|**空间开销**|递归栈 $O(N)$|Map + Set + Stack，常数项较大|
|**面试评价**|**最优解，体现算法功底**|思路清晰，体现工程思维|

建议：

先掌握解法一（递归法），这是面试的标准答案。

如果面试官问：“二叉树没有父指针很麻烦，如果这是一颗包含父指针的树（Node有parent属性），你怎么做？” 此时你就可以自信地拿出解法二的逻辑（转化为链表相交）来回答。