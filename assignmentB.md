# Assignment #B: 图论和树算

Updated 1600 GMT+8 Apr 28, 2024

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

### 28170: 算鹰

dfs, http://cs101.openjudge.cn/practice/28170/



思路：

dfs的时候，visited列表修改值为当前已经计数的连通块个数。

代码

```python
# 
def in_map(x, y):
    return 0 <= x <= 9 and 0 <= y <= 9

def dfs(line, column, ans):
    directions = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    for dx, dy in directions:
        x = line + dx
        y = column + dy
        if in_map(x, y):
            if visited[x][y] == 0 and graph[x][y] == '.':
                visited[x][y] = ans
                dfs(x, y, ans)
    return

graph = []
visited = [[0 for _ in range(10)] for __ in range(10)]
for _ in range(10):
    lst = list(input())
    graph.append(lst)
ans = 1
for line in range(10):
    for column in range(10):
        if visited[line][column] == 0 and graph[line][column] == '.':
            visited[line][column] = ans
            dfs(line, column, ans)
            ans += 1
print(ans - 1)
```



代码运行截图 ==（至少包含有"Accepted"）==

20min

![image-20240430153504564](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240430153504564.png)



### 02754: 八皇后

dfs, http://cs101.openjudge.cn/practice/02754/



思路：

查找四个方向。注意dfs后需要回溯（清除标记）

代码

```python
# 
def in_map(x, y):
    return 0 <= x <= 7 and 0 <= y <= 7

def check(line, row):
    for i in range(8):
        if queen[line][i] == 1 or queen[i][row] == 1:
            return False
    x1 = line + 1
    y1 = row + 1
    x2 = line - 1
    y2 = row - 1
    x3 = line + 1
    y3 = row - 1
    x4 = line - 1
    y4 = row + 1
    while in_map(x1, y1):
        if queen[x1][y1] == 1:
            return False
        x1 += 1
        y1 += 1
    while in_map(x2, y2):
        if queen[x2][y2] == 1:
            return False
        x2 -= 1
        y2 -= 1
    while in_map(x3, y3):
        if queen[x3][y3] == 1:
            return False
        x3 += 1
        y3 -= 1
    while in_map(x4, y4):
        if queen[x4][y4] == 1:
            return False
        x4 -= 1
        y4 += 1
    return True

def dfs(line):
    if line == 8:
        ans = 0
        for i in range(8):
            ans = ans * 10 + (queen[i].index(1) + 1)
        lst.append(ans)
        return
    for j in range(8):
        if check(line, j):
            queen[line][j] = 1
            dfs(line + 1)
            queen[line][j] = 0

n = int(input())
lst = []
queen = [[0 for _ in range(8)] for __ in range(8)]
dfs(0)
for _ in range(n):
    s = int(input())
    print(lst[s - 1])
```



代码运行截图 ==（至少包含有"Accepted"）==

20min

![image-20240505164639877](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240505164639877.png)



### 03151: Pots

bfs, http://cs101.openjudge.cn/2024sp_routine/03151/



思路：



代码

```python
# 

```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==





### 05907: 二叉树的操作

http://cs101.openjudge.cn/dsapre/05907/



思路：



代码

```python
# 

```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==







### 18250: 冰阔落 I

Disjoint set, http://cs101.openjudge.cn/practice/18250/



思路：

并查集，注意本题的union操作要求有序。

代码

```python
# 
def find(x):
    if parent[x] != x:
        parent[x] = find(parent[x])
    return parent[x]

def union(x, y):
    root_x = find(x)
    root_y = find(y)
    if root_x != root_y:
        parent[root_y] = root_x

while True:
    try:
        n, m = map(int, input().split())
        parent = list(range(n + 1))

        for _ in range(m):
            a, b = map(int, input().split())
            if find(a) == find(b):
                print('Yes')
            else:
                print('No')
                union(a, b)

        unique_parents = set(find(x) for x in range(1, n + 1))
        ans = sorted(unique_parents)
        print(len(ans))
        print(*ans)
    except EOFError:
        break
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

25min

![image-20240506183441716](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240506183441716.png)



### 05443: 兔子与樱花

http://cs101.openjudge.cn/practice/05443/



思路：



代码

```python
# 
import heapq
def dijkstra(graph, start, end):
    if start == end:
        return []
    dist = {i: (1000000000000, []) for i in graph}
    dist[start] = (0, [start])
    pos = []
    heapq.heappush(pos, (0, start, []))
    while pos:
        dist1, current, path = heapq.heappop(pos)
        for (next, dist2) in graph[current].items():
            if dist2 + dist1 < dist[next][0]:
                dist[next] = (dist2 + dist1, path + [next])
                heapq.heappush(pos, (dist1 + dist2, next, path+[next]))
    return dist[end][1]

P = int(input())
graph = {input(): {} for _ in range(P)}
Q = int(input())
for _ in range(Q):
    place1, place2, dist = input().split()
    graph[place1][place2] = graph[place2][place1] = int(dist)
R = int(input())
for _ in range(R):
    start, end = input().split()
    path = dijkstra(graph, start, end)
    s = start
    current = start
    for i in path:
        s += f'->({graph[current][i]})->{i}'
        current = i
    print(s)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

30min

![image-20240506185202234](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240506185202234.png)



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

列表元素为int类型时，print(*lst)可以达到join函数的效果。

放完假了，该补前面欠下的债了。



