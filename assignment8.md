# Assignment #8: 图论：概念、遍历，及 树算

Updated 1150 GMT+8 Apr 8, 2024

2024 spring, Complied by ==同学的姓名、院系==



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

### 19943: 图的拉普拉斯矩阵

matrices, http://cs101.openjudge.cn/practice/19943/



思路：

注意到D的i，i元恰好是A的第i行中，1的个数

代码

```python
# 
n, m = map(int, input().split())
D, A, L = [[0 for _ in range(n)] for __ in range(n)], [[0 for _ in range(n)] for i in range(n)], [[0 for _ in range(n)] for j in range(n)]
for _ in range(m):
    a, b = map(int, input().split())
    A[a][b] = 1
    A[b][a] = 1
for k in range(n):
    D[k][k] = A[k].count(1)
for i in range(n):
    for j in range(n):
        L[i][j] = D[i][j] - A[i][j]
    print(' '.join(map(str, L[i])))
```



代码运行截图 ==（至少包含有"Accepted"）==

15min完成

![image-20240413125546044](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240413125546044.png)



### 18160: 最大连通域面积

matrix/dfs similar, http://cs101.openjudge.cn/practice/18160



思路：

向8个方向dfs+利用visited列表剪枝

代码

```python
# 
def dfs(graph, i, j, visited):
    cnt = 1
    if i < 0 or j < 0 or i >= n or j >= m or visited[i][j] or graph[i][j] == '.':
        return 0
    visited[i][j] = 1
    cnt += 1
    cnt += dfs(graph, i - 1, j, visited)
    cnt += dfs(graph, i - 1, j + 1, visited)
    cnt += dfs(graph, i - 1, j - 1, visited)
    cnt += dfs(graph, i, j + 1, visited)
    cnt += dfs(graph, i, j - 1, visited)
    cnt += dfs(graph, i + 1, j, visited)
    cnt += dfs(graph, i + 1, j + 1, visited)
    cnt += dfs(graph, i + 1, j - 1, visited)
    return cnt

t = int(input())
for _ in range(t):
    n, m = map(int, input().split())
    graph, ans = [], [0]
    for k in range(n):
        graph.append(list(input()))
    visited = [[0 for _ in range(m)] for __ in range(n)]
    for i in range(n):
        for j in range(m):
            if graph[i][j] != '.':
                cnt = dfs(graph, i, j, visited) // 2
                ans.append(cnt)
    print(max(ans))
```



代码运行截图 ==（至少包含有"Accepted"）==

20min

![image-20240413125706655](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240413125706655.png)

### sy383: 最大权值连通块

https://sunnywhy.com/sfbj/10/3/383



思路：

和上题类似，仅把连通块面积变成权值

代码

```python
# 
def dfs(idx, visited):
    cnt = 0
    if visited[idx]:
        return 0
    visited[idx] = 1
    cnt += value_lst[idx]
    for vertex in graph[idx]:
        cnt += dfs(vertex, visited)
    return cnt

n, m = map(int, input().split())
value_lst = list(map(int, input().split()))
graph = [[] for _ in range(n)]

for _ in range(m):
    a, b = map(int, input().split())
    graph[a].append(b)
    graph[b].append(a)

visited = [0] * n
ans = [0]
for idx in range(n):
    ans.append(dfs(idx, visited))
print(max(ans))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

20min完成

![image-20240413125843586](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240413125843586.png)



### 03441: 4 Values whose Sum is 0

data structure/binary search, http://cs101.openjudge.cn/practice/03441



思路：

二分，要平衡好时间和空间

代码

```python
# 
from collections import Counter
from itertools import product
n = int(input())
A, B, C, D = [], [], [], []
for i in range(n):
    a, b, c, d = map(int, input().split())
    A.append(a)
    B.append(b)
    C.append(c)
    D.append(d)
ab_sum = Counter(map(sum, product(A, B)))
cnt = 0
for cd_sum in map(sum, product(C, D)):
    cnt += ab_sum.get(-cd_sum, 0)
print(cnt)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

30min完成

![image-20240413133929370](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240413133929370.png)

### 04089: 电话号码

trie, http://cs101.openjudge.cn/practice/04089/



思路：

排序之后，前缀电话号码必然相邻出现（但是做了半个小时都WA，最后发现应该输出YES而不是Yes）

代码

```python
# 
n = int(input())
for _ in range(n):
    m = int(input())
    lst = []
    ans = 1
    for i in range(m):
        lst.append(input())
    lst.sort()
    for i in range(m - 1):
        if len(lst[i + 1]) >= len(lst[i]):
            if lst[i] == lst[i + 1][:len(lst[i]):]:
                ans = 0
        if not ans:
            break
    print('YES' if ans else 'NO')
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

30min完成

![image-20240413134119429](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240413134119429.png)

### 04082: 树的镜面映射

http://cs101.openjudge.cn/practice/04082/



思路：

对着群里同学的代码才慢慢理清楚了题意……

代码

```python
# 
class TreeNode:
    def __init__(self, value = None):
        self.value = value
        self.left = None
        self.right = None

def BuildTree(lst, idx):
    node = TreeNode()
    node.value = lst[idx][0]
    if lst[idx][1] == '0':
        idx += 1
        child, idx = BuildTree(lst, idx)
        node.left = child
        idx += 1
        child, idx = BuildTree(lst, idx)
        node.right = child
    return node, idx

def PrintTree(root):
    global res
    res.append(root.value)
    if root.right and root.right.value != '$':
        PrintTree(root.right)
    else:
        res.reverse()
        ans.extend(res)
        res = []

    if root.left and root.left.value != '$':
        PrintTree(root.left)
    else:
        res.reverse()
        ans.extend(res)
        res = []

n = int(input())
root, idx = BuildTree(list(input().split()), 0)
ans, res = [], []
PrintTree(root)
print(' '.join(ans))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

群里说加pylint，但是却WA了，为什么呀qwq

![image-20240413141008727](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240413141008727.png)![image-20240413141026087](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240413141026087.png)



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

03441 4 values whose sum is 0：虽然 product(A,B) 和 [(x, y) for x in A for y in B]意义一样，但是却有着不同的复杂度。product省空间费时间；列表生成式省时间费空间。（使用product就AC，时间4000多ms；但用列表生成式，时间虽然只有900多不到1000ms，但是空间复杂度大，最后MLE）

有些题目没能很快理解到题目的意思……



