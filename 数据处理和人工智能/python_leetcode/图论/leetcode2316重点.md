```python
class Solution:

    def countPairs(self, n: int, edges: List[List[int]]) -> int:

        graph=[[] for _ in range(n)]

        for u,v in edges:

            graph[u].append(v)

            graph[v].append(u)

        visit=[False]*n

  

        def dfs(current_node:int)->int:

            visit[current_node]=True

            size=1

            for neighbor in graph[current_node]:

                if not visit[neighbor]:

                    size+=dfs(neighbor)

            return size

  

        ans=total=0

        for i in range(n):

            if not visit[i]:

                size=dfs(i)

                ans+=size*total

                total+=size

        return ans
```


```python
ans=total=0

        for i in range(n):

            if not visit[i]:

                size=dfs(i)

                ans+=size*total

                total+=size

```
重点代码
