# Assignment #F: All-Killed 满分

Updated 1844 GMT+8 May 20, 2024

2024 spring, Complied by ==王伟圣 数学科学学院==



**说明：**

1）请把每个题目解题思路（可选），源码Python, 或者C++（已经在Codeforces/Openjudge上AC），截图（包含Accepted），填写到下面作业模版中（推荐使用 typora https://typoraio.cn ，或者用word）。AC 或者没有AC，都请标上每个题目大致花费时间。

2）提交时候先提交pdf文件，再把md或者doc文件上传到右侧“作业评论”。Canvas需要有同学清晰头像、提交文件有pdf、"作业评论"区有上传的md或者doc附件。

3）如果不能在截止前提交作业，请写明原因。



**编程环境**

==（请改为同学的操作系统、编程环境等）==

操作系统：macOS Ventura 13.4.1 (c)

Python编程环境：Spyder IDE 5.2.2, PyCharm 2023.1.4 (Professional Edition)

C/C++编程环境：Mac terminal vi (version 9.0.1424), g++/gcc (Apple clang version 14.0.3, clang-1403.0.22.14.1)



## 1. 题目

### 22485: 升空的焰火，从侧面看

http://cs101.openjudge.cn/practice/22485/



思路：



代码

```python
# 
def dfs(node, level):
    if ans[level] == 0:
        ans[level] = node
    for next in tree[node][::-1]:
        if next != -1:
            dfs(next, level + 1)
n = int(input())
tree = {}
ans = [0] * n
for i in range(n):
    tree[i + 1]=list(map(int, input().split()))
dfs(1, 0)
res = []
for i in ans:
    if i:
        res.append(i)
    else:
        break
print(*res)
```



代码运行截图 ==（至少包含有"Accepted"）==

![image-20240528204249936](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240528204249936.png)



### 28203:【模板】单调栈

http://cs101.openjudge.cn/practice/28203/



思路：



代码

```python
# 
n = int(input())
ans = [0 for _ in range(n)]
lst = list(map(int, input().split()))
stack = []
i = 0
while i < n:
    while stack and lst[i] > lst[stack[-1]]:
        ans[stack.pop()] = i + 1
    stack.append(i)
    i += 1
print(*ans)
```



代码运行截图 ==（至少包含有"Accepted"）==

![image-20240528174711338](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240528174711338.png)



### 09202: 舰队、海域出击！

http://cs101.openjudge.cn/practice/09202/



思路：

拓扑排序检查有向图是否存在环。它的基本思想是通过计算每个顶点的入度，并从入度为 0 的顶点开始进行深度优先搜索（DFS）。在 DFS 过程中，每遍历到一个顶点时，将其标记为已访问，并将其所有邻居的入度减一。如果邻居的入度减为 0，则继续对邻居进行 DFS。如果最终所有顶点都被访问到，则图中不存在环；否则，存在环。

代码

```python
# 
from collections import defaultdict

def dfs(p):
    vis[p] = True
    for q in graph[p]:
        in_degree[q] -= 1
        if in_degree[q] == 0:
            dfs(q)

for _ in range(int(input())):
    n, m = map(int, input().split())
    graph = defaultdict(list)
    in_degree = [0] * (n + 1)
    vis = [False] * (n + 1) 
    for _ in range(m):
        x, y = map(int, input().split())
        graph[x].append(y)
        in_degree[y] += 1
    for k in range(1, n + 1):  
        if in_degree[k] == 0 and not vis[k]:  
            dfs(k)
    flag = any(not vis[i] for i in range(1, n + 1))  
    print('Yes' if flag else 'No')
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240528203937873](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240528203937873.png)



### 04135: 月度开销

http://cs101.openjudge.cn/practice/04135/



思路：



代码

```python
# 
def check(x):
    num, s = 1, 0
    for i in range(n):
        if s + expenditure[i] > x:
            s = expenditure[i]
            num += 1
        else:
            s += expenditure[i]

    return [False, True][num > m]

n, m = map(int, input().split())
expenditure = []
for _ in range(n):
    expenditure.append(int(input()))
lo = max(expenditure)
hi = sum(expenditure) + 1
ans = 1
while lo < hi:
    mid = (lo + hi) // 2
    if check(mid):
        lo = mid + 1
    else:
        ans = mid
        hi = mid
print(ans)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240528205254238](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240528205254238.png)



### 07735: 道路

http://cs101.openjudge.cn/practice/07735/



思路：



代码

```python
# 
import heapq

def dijkstra(g):
    while pq:
        dist,node,fee = heapq.heappop(pq)
        if node == n-1 :
            return dist
        for nei,w,f in g[node]:
            n_dist = dist + w
            n_fee = fee + f
            if n_fee <= k:
                dists[nei] = n_dist
                heapq.heappush(pq,(n_dist,nei,n_fee))
    return -1

k,n,r = int(input()),int(input()),int(input())
g = [[] for _ in range(n)]
for i in range(r):
    s,d,l,t = map(int,input().split())
    g[s-1].append((d-1,l,t)) #node,dist,fee

pq = [(0,0,0)] #dist,node,fee
dists = [float('inf')] * n
dists[0] = 0
spend = 0

result = dijkstra(g)
print(result)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240528210120570](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240528210120570.png)



### 01182: 食物链

http://cs101.openjudge.cn/practice/01182/



思路：



代码

```python
# 
class DisjointSet:
    def __init__(self, n):
        self.parent = [i for i in range(3 * n + 1)]
        self.rank = [0] * (3 * n + 1)

    def find(self, u):
        if self.parent[u] != u:
            self.parent[u] = self.find(self.parent[u])
        return self.parent[u]

    def union(self, u, v):
        pu, pv = self.find(u), self.find(v)
        if pu == pv:
            return False
        if self.rank[pu] > self.rank[pv]:
            self.parent[pv] = pu
        elif self.rank[pu] < self.rank[pv]:
            self.parent[pu] = pv
        else:
            self.parent[pv] = pu
            self.rank[pu] += 1
        return True

def find_disjoint_set(x, n):
    if x > n:
        return False
    return True
def is_valid(n, k, statements):
    dsu = DisjointSet(n)
    false_count = 0
    for d, x, y in statements:
        if not find_disjoint_set(x, n) or not find_disjoint_set(y, n):
            false_count += 1
            continue
        if d == 1:
            if dsu.find(x) == dsu.find(y + n) or dsu.find(x) == dsu.find(y + 2 * n):
                false_count += 1
            else:
                dsu.union(x, y)
                dsu.union(x + n, y + n)
                dsu.union(x + 2 * n, y + 2 * n)
        else:
            if dsu.find(x) == dsu.find(y) or dsu.find(x + 2*n) == dsu.find(y):
                false_count += 1
            else:
                dsu.union(x + n, y)
                dsu.union(x, y + 2 * n)
                dsu.union(x + 2 * n, y + n)
    return false_count

n, k = map(int, input().split())
statements = []
for _ in range(k):
    d, x, y = map(int, input().split())
    statements.append((d, x, y))
result = is_valid(n, k, statements)
print(result)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240528210738124](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240528210738124.png)



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

准备完善&复习cheat paper了，期望机考能够多出一些模板题



