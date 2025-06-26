好的，这里提供三种不同的方法来找出有向无环图（DAG）中从节点 0 到节点 n-1 的所有路径。每种方法都会附上详细的解释和 Python 代码实现。

### 问题描述

给定一个 `graph`，其中 `graph[i]` 是一个列表，包含了从节点 `i` 可以直接到达的所有节点。你需要找到从起始节点 `0` 到目标节点 `n-1` 的所有可能路径。

**示例:** 输入: `graph = [[1,2],[3],[3],[]]` 输出: `[[0,1,3],[0,2,3]]` 解释: 在这个图中，`n=4`。有两条从节点 0 到节点 3 的路径：`0 -> 1 -> 3` 和 `0 -> 2 -> 3`。

---

### 方法一：深度优先搜索（DFS）与回溯

这是解决此类路径查找问题的最经典和直观的方法。我们从起始节点 `0` 开始，沿着一条边走到下一个节点，再从那个节点继续探索，直到到达目标节点 `n-1` 或无法再前进为止。

**算法思想：**

1. **递归函数**：定义一个递归函数 `dfs(currentNode, currentPath)`，其中 `currentNode` 是当前访问的节点，`currentPath` 是从起点 `0` 到 `currentNode` 的路径。
    
2. **路径记录**：将当前节点 `currentNode` 添加到 `currentPath` 中。
    
3. **到达终点**：如果 `currentNode` 是目标节点 `n-1`，说明我们找到了一条完整路径。将 `currentPath` 的一个副本添加到最终结果列表中。
    
4. **继续探索**：遍历 `currentNode` 的所有邻居节点。对于每个邻居 `neighbor`，以它为新节点递归调用 `dfs` 函数。
    
5. **回溯**：当从一个节点的邻居节点递归返回后，需要将该节点从 `currentPath` 中移除。这一步至关重要，被称为“回溯”。它保证了在探索完一条分支后，可以返回到上一个节点，继续探索其他分支。
    

**Python 实现:**

Python

```python
from typing import List

class SolutionDFS:
    def allPathsSourceTarget(self, graph: List[List[int]]) -> List[List[int]]:
        """
        方法一：深度优先搜索（DFS）与回溯
        """
        target = len(graph) - 1
        results = []
        path = [0] # 路径从节点 0 开始

        def dfs(current_node):
            # 如果当前节点是目标节点，则找到一条路径
            if current_node == target:
                # 添加路径的副本
                results.append(list(path))
                return

            # 遍历当前节点的所有邻居
            for neighbor in graph[current_node]:
                # 做出选择：将邻居添加到路径中
                path.append(neighbor)
                # 递归探索
                dfs(neighbor)
                # 撤销选择（回溯）：将邻居从路径中移除
                path.pop()

        # 从节点 0 开始搜索
        dfs(0)
        return results

# 示例
graph = [[1,2],[3],[3],[]]
solver_dfs = SolutionDFS()
print(f"DFS 解法: {solver_dfs.allPathsSourceTarget(graph)}")
# 输出: DFS 解法: [[0, 1, 3], [0, 2, 3]]

graph2 = [[4,3,1],[3,2,4],[3],[4],[]]
print(f"DFS 解法: {solver_dfs.allPathsSourceTarget(graph2)}")
# 输出: DFS 解法: [[0, 4], [0, 3, 4], [0, 1, 3, 4], [0, 1, 2, 3, 4], [0, 1, 4]]
```

---

### 方法二：广度优先搜索（BFS）

虽然标准的广度优先搜索（BFS）通常用于寻找最短路径，但通过稍加改造，我们也可以用它来寻找所有路径。关键在于，队列中存储的不再是单个节点，而是从起点到当前节点的完整路径。

**算法思想：**

1. **路径队列**：创建一个队列，并在其中放入只包含起始节点 `0` 的初始路径 `[0]`。
    
2. **迭代处理**：当队列不为空时，循环执行以下操作： a. 从队列头部取出一个路径 `currentPath`。 b. 获取该路径的最后一个节点 `lastNode`。 c. **到达终点**：如果 `lastNode` 是目标节点 `n-1`，则将 `currentPath` 添加到最终结果列表中。 d. **扩展路径**：否则，遍历 `lastNode` 的所有邻居节点。对于每个邻居 `neighbor`，创建一个新路径（即 `currentPath` 加上 `neighbor`），并将这个新路径加入队列。
    
3. **结束**：当队列为空时，表示所有可能的路径都已被探索完毕。
    

**Python 实现:**

Python

```python
from collections import deque
from typing import List

class SolutionBFS:
    def allPathsSourceTarget(self, graph: List[List[int]]) -> List[List[int]]:
        """
        方法二：广度优先搜索（BFS）
        """
        target = len(graph) - 1
        results = []
        
        # 队列中存储的是从起点到当前节点的路径
        queue = deque([[0]])

        while queue:
            current_path = queue.popleft()
            last_node = current_path[-1]

            # 如果路径的最后一个节点是目标节点，则保存路径
            if last_node == target:
                results.append(current_path)
            else:
                # 将邻居节点加入到路径中，并将新路径入队
                for neighbor in graph[last_node]:
                    new_path = current_path + [neighbor]
                    queue.append(new_path)
        
        return results

# 示例
graph = [[1,2],[3],[3],[]]
solver_bfs = SolutionBFS()
print(f"BFS 解法: {solver_bfs.allPathsSourceTarget(graph)}")
# 输出: BFS 解法: [[0, 1, 3], [0, 2, 3]]

graph2 = [[4,3,1],[3,2,4],[3],[4],[]]
print(f"BFS 解法: {solver_bfs.allPathsSourceTarget(graph2)}")
# 输出: BFS 解法: [[0, 4], [0, 3, 4], [0, 1, 4], [0, 1, 3, 4], [0, 1, 2, 3, 4]]
```

---

### 方法三：动态规划与备忘录（自顶向下）

我们可以换一个角度思考问题。要求从 `0` 到 `n-1` 的所有路径，可以分解为：对于 `0` 的每个邻居 `v`，我们先找到从 `v` 到 `n-1` 的所有路径，然后在这些路径的开头都加上 `0`。

**算法思想:**

1. **定义子问题**：定义一个函数 `find_paths_from(node)`，它的功能是返回从 `node` 到 `n-1` 的所有路径。
    
2. **备忘录**：使用一个哈希表（或字典）`memo` 来存储已经计算过的 `find_paths_from(node)` 的结果，避免重复计算。
    
3. **基本情况（Base Case）**：如果 `node` 就是目标节点 `n-1`，那么从它到自身的路径只有一条，即 `[[n-1]]`。
    
4. **递归关系**：对于任意节点 `node`，我们遍历它的所有邻居 `neighbor`。对每个 `neighbor`，我们递归调用 `find_paths_from(neighbor)` 来获取从 `neighbor` 出发的所有路径。然后，将 `node` 添加到这些子路径的前面，形成从 `node` 出发的新路径。
    
5. **主函数**：最后，调用 `find_paths_from(0)` 即可得到最终答案。
    

这种方法本质上也是深度优先搜索，但通过备忘录（memoization）优化了对重复子问题的计算，因此也属于动态规划的一种形式。

**Python 实现:**

Python

```python
from typing import List, Dict

class SolutionDP:
    def allPathsSourceTarget(self, graph: List[List[int]]) -> List[List[int]]:
        """
        方法三：动态规划与备忘录
        """
        target = len(graph) - 1
        # 备忘录，存储从节点 i 到 target 的所有路径
        memo = {}

        def find_paths_from(current_node: int) -> List[List[int]]:
            # 如果结果已经计算过，直接返回
            if current_node in memo:
                return memo[current_node]

            # 基本情况：如果当前节点是目标节点
            if current_node == target:
                return [[target]]

            # 递归计算
            paths = []
            for neighbor in graph[current_node]:
                # 找到从邻居到目标的所有路径
                sub_paths = find_paths_from(neighbor)
                # 在这些子路径前加上当前节点
                for path in sub_paths:
                    paths.append([current_node] + path)
            
            # 存入备忘录并返回结果
            memo[current_node] = paths
            return paths

        return find_paths_from(0)

# 示例
graph = [[1,2],[3],[3],[]]
solver_dp = SolutionDP()
print(f"DP 解法: {solver_dp.allPathsSourceTarget(graph)}")
# 输出: DP 解法: [[0, 1, 3], [0, 2, 3]]

graph2 = [[4,3,1],[3,2,4],[3],[4],[]]
print(f"DP 解法: {solver_dp.allPathsSourceTarget(graph2)}")
# 输出: DP 解法: [[0, 4], [0, 3, 4], [0, 1, 3, 4], [0, 1, 2, 3, 4], [0, 1, 4]]
```

### 三种方法总结

|方法|核心思想|优点|缺点|
|---|---|---|---|
|**DFS 与回溯**|递归地探索图，通过回溯来尝试所有分支。|实现简单直观，内存效率较高（只维护一条当前路径）。|在有大量重复子路径的图中，可能会有冗余计算。|
|**BFS**|使用队列存储完整路径，逐层扩展。|实现相对简单，可以保证找到的路径按长度递增（但不保证整体有序）。|内存消耗可能较大，因为队列中需要存储所有中间路径。|
|**DP 与备忘录**|自顶向下递归，并缓存子问题的解。|效率最高，尤其是在图中存在大量汇合点（即多个路径经过同一个中间节点）时，能避免大量重复计算。|实现稍复杂，需要额外空间存储备忘录。|

Export to Sheets

对于这个问题，由于是有向无环图，三种方法都能有效解决。在性能上，动态规划（带备忘录的DFS）通常是最佳选择。