# Assignment #A: 图论：遍历，树算及栈

Updated 2018 GMT+8 Apr 21, 2024

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

### 20743: 整人的提词本

http://cs101.openjudge.cn/practice/20743/



思路：

从左到右遍历，把所有字符压入栈中。每当遇到一个右括号，就从栈顶往下遍历，依次弹出，遇到左括号时，反向压回去。这样能在最后一个右括号的位置，把所有非括号字符弹出。

代码

```python
# 
s = input()
stack = []
for char in s:
    if char == ')':
        stack1 = []
        while stack and stack[-1] != '(':
            stack1.append(stack.pop())
        if stack:
            stack.pop()
        stack.extend(stack1)
    else:
        stack.append(char)
print(''.join(stack))
```



代码运行截图 ==（至少包含有"Accepted"）==

5min完成

![image-20240423204657164](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240423204657164.png)



### 02255: 重建二叉树

http://cs101.openjudge.cn/practice/02255/



思路：

很经典的前中序表达式建树+后序遍历。

代码

```python
# 
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def BuildTree(pre, mid):
    if not pre or not mid:
        return None
    root_val = pre[0]
    root = BinaryTreeNode(root_val)
    root_idx = mid.index(root_val)
    root.left = BuildTree(pre[1:root_idx + 1:], mid[:root_idx:])
    root.right = BuildTree(pre[root_idx + 1::], mid[root_idx + 1::])
    return root

def postorder(root):
    left = postorder(root.left) if root.left else []
    right = postorder(root.right) if root.right else []
    return left + right + [root.value]

while True:
    try:
        pre, mid = input().split()
        root = BuildTree(list(pre), list(mid))
        print(''.join(postorder(root)))
    except EOFError:
        break
```



代码运行截图 ==（至少包含有"Accepted"）==

10min完成

![image-20240423205705917](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240423205705917.png)

### 01426: Find The Multiple

http://cs101.openjudge.cn/practice/01426/

要求用bfs实现



思路：

每次在已有的数之后添0或添1，相当于二叉树分支，广搜的时候就把每个分支加入待搜索的列表。搜到答案就立刻break。

技巧：visited列表用于剪枝，储存余数，便于快速运算

代码

```python
# 
from collections import deque
while True:
    n = int(input())
    if n == 0:
        break
    q = deque()
    q.append((1 % n, 1))
    visited = [1 % n]
    while q:
        remain, num = q.popleft()
        if remain == 0:
            print(num)
            break
        for number in [0, 1]:
            new_remain = (remain * 10 + number) % n
            new_num = num * 10 + number
            if new_remain not in visited:
                visited.append(new_remain)
                q.append((new_remain, new_num))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

10min完成

![image-20240423210754848](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240423210754848.png)

### 04115: 鸣人和佐助

bfs, http://cs101.openjudge.cn/practice/04115/



思路：

用一个三维数组存储该点在某数量的查克拉下是否被访问。因为会存在消耗不同查克拉，都能走到同一位置的情况。

bfs整体思路还是一样的。

代码

```python
# 
from collections import deque
class Node:
    def __init__(self, x = 0, y = 0, time = 0, t = 0):
        self.x = x
        self.y = y
        self.time = time
        self.t = t

def in_map(x, y):
    if x < 0 or y < 0 or x >= m or y >= n:
        return 0
    else:
        return 1

def bfs(startx, starty, endx, endy, t):
    directions = [(0, -1), (0, 1), (-1, 0), (1, 0)]
    temp = Node(startx, starty, 0, t)
    q = deque()
    q.append(temp)
    visited = [[[0 for _ in range(t + 1)] for __ in range(n)] for ___ in range(m)]
    while q:
        point = q.popleft()
        if point.x == endx and point.y == endy:
            return point.time
        for dx, dy in directions:
            xx = point.x + dx
            yy = point.y + dy
            if in_map(xx, yy):
                if graph[xx][yy] == '#' and not visited[xx][yy][point.t - 1] and point.t >= 1:
                    visited[xx][yy][point.t - 1] = 1
                    q.append(Node(xx, yy, point.time + 1, point.t - 1))
                elif graph[xx][yy] != '#' and not visited[xx][yy][point.t]:
                    visited[xx][yy][point.t] = 1
                    q.append(Node(xx, yy, point.time + 1, point.t))
    return -1

m, n, t = map(int, input().split())
graph = []
startx, starty, endx, endy = 0, 0, 0, 0
for i in range(m):
    lst = list(input())
    if '@' in lst:
        startx = i
        starty = lst.index('@')
    if '+' in lst:
        endx = i
        endy = lst.index('+')
    graph.append(lst)
print(bfs(startx, starty, endx, endy, t))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240427141044566](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240427141044566.png)



### 20106: 走山路

Dijkstra, http://cs101.openjudge.cn/practice/20106/



思路：



代码

```python
# 

```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==





### 05442: 兔子与星空

Prim, http://cs101.openjudge.cn/practice/05442/



思路：



代码

```python
# 
import heapq
def prim(graph, start):
    mst = []
    used = set(start)
    edges = [(cost, start, to) for to, cost in graph[start].items()]
    heapq.heapify(edges)
    while edges:
        cost, frm, to = heapq.heappop(edges)
        if to not in used:
            used.add(to)
            mst.append((frm, to, cost))
            for to_next, cost2 in graph[to].items():
                if to_next not in used:
                    heapq.heappush(edges, (cost2, to, to_next))
    return mst

n = int(input())
graph = {chr(i+65): {} for i in range(n)}
for i in range(n-1):
    data = input().split()
    star = data[0]
    m = int(data[1])
    for j in range(m):
        to_star = data[2+j*2]
        cost = int(data[3+j*2])
        graph[star][to_star] = cost
        graph[to_star][star] = cost
mst = prim(graph, 'A')
print(sum(x[2] for x in mst))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240428164801641](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240428164801641.png)



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

三维数组很坑！对于

```python
#
lst = [[[0 for _ in range(n)] for __ in range(m)] for ___ in range(k)]
lst[a][b][c]
##对应a,b,c的范围是0~k-1，0~m-1，0~n-1，不是从左往右的一一对应关系！
```

并且for i in range(0)不会进循环。

以及终于考完期中啦！好好的玩一个五一节！

ps：20106和dijkstra计划五一过完，回归学习强度之后再补。



