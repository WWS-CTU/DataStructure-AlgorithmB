# Assignment #D: May月考

Updated 1654 GMT+8 May 8, 2024

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

### 02808: 校门外的树

http://cs101.openjudge.cn/practice/02808/



思路：

签到题

代码

```python
# 
l, m = map(int, input().split())
lst = [1] * (l + 1)
for _ in range(m):
    start, finish = map(int, input().split())
    for i in range(start, finish + 1):
        lst[i] = 0
print(lst.count(1))
```



代码运行截图 ==（至少包含有"Accepted"）==

![image-20240512124157375](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240512124157375.png)



### 20449: 是否被5整除

http://cs101.openjudge.cn/practice/20449/



思路：

签到题

代码

```python
# 
s = input()
answer = ''
number = 0
for i in range(len(s)):
    if i == 0:
        number = int(s[0])
        a1 = '1' if s[0] == '0' else '0'
        answer = answer + a1
    else:
        number = number * 2 + int(s[i])
        if number % 5 == 0:
            answer = answer + '1'
        else:
            answer = answer + '0'
print(answer)
```



代码运行截图 ==（至少包含有"Accepted"）==

![image-20240512124819302](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240512124819302.png)



### 01258: Agri-Net

http://cs101.openjudge.cn/practice/01258/



思路：

经典的kruskal算法解决图的mst问题。

代码

```python
# 
class DisjointSet:
    def __init__(self, num_vertices):
        self.parent = list(range(num_vertices))
        self.rank = [0] * num_vertices

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def is_union(self, x, y):
        xr = self.find(x)
        yr = self.find(y)
        if xr == yr:
            return False
        elif self.rank[xr] < self.rank[yr]:
            self.parent[xr] = yr
        elif self.rank[xr] > self.rank[yr]:
            self.parent[yr] = xr
        else:
            self.parent[yr] = xr
            self.rank[xr] += 1
        return True

def kruskal(n, edges):
    dsu = DisjointSet(n)
    mst_weight = 0
    for weight, u, v in sorted(edges):
        if dsu.is_union(u, v):
            mst_weight += weight
    return mst_weight

while True:
    try:
        n = int(input().strip())
        edges = []
        for i in range(n):
            row = list(map(int, input().split()))
            for j in range(i + 1, n):
                if row[j] != 0:
                    edges.append((row[j], i, j))
        print(kruskal(n, edges))
    except EOFError:
        break
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240512130154677](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240512130154677.png)



### 27635: 判断无向图是否连通有无回路(同23163)

http://cs101.openjudge.cn/practice/27635/



思路：

连通：使用栈bfs

环：dfs

代码

```python
# 
def is_connected(graph):
    visited = [0] * n
    stack = [0]
    visited[0] = 1
    while stack:
        num = stack.pop()
        for vertex in graph[num]:
            if not visited[vertex]:
                stack.append(vertex)
                visited[vertex] = 1
    for vertex in visited:
        if not vertex:
            return False
    return True

def has_loop(graph):
    visited = [False] * n
    for node in range(n):
        if not visited[node]:
            if dfs(node, visited, -1):
                return True
    return False

def dfs(node, visited, parent):
    visited[node] = True
    for neighbor in graph[node]:
        if not visited[neighbor]:
            if dfs(neighbor, visited, node):
                return True
        elif parent != neighbor:
            return True
    return False

n, m = map(int, input().split())
graph = [[] for i in range(n)]
for _ in range(m):
    u, v = map(int, input().split())
    graph[u].append(v)
    graph[v].append(u)
connected = 'connected:yes' if is_connected(graph) else 'connected:no'
loop = 'loop:yes' if has_loop(graph) else 'loop:no'
print(connected)
print(loop)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240512131751391](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240512131751391.png)





### 27947: 动态中位数

http://cs101.openjudge.cn/practice/27947/



思路：



代码

```python
# 

```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==





### 28190: 奶牛排队

http://cs101.openjudge.cn/practice/28190/



思路：



代码

```python
# 

```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==





## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

只做得起E/M级别的题ww



