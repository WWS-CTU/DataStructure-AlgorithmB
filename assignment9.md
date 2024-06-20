# Assignment #9: 图论：遍历，及 树算

Updated 1739 GMT+8 Apr 14, 2024

2024 spring, Complied by 王伟圣 数学科学学院



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

### 04081: 树的转换

http://cs101.openjudge.cn/dsapre/04081/



思路：

这里的NODE需要记录节点的第一个孩子和该节点的下一个兄弟节点

第一次WA是因为忘记最后print的时候，depth2要减一（通过尝试，猜测要减1）

代码

```python
# 
class TreeNode:
    def __init__(self):
        self.children = []
        self.first_child = None
        self.next_brother = None

def BuildTree(sequence):
    root = TreeNode()
    stack = [root]
    depth = 0
    for action in sequence:
        current_node = stack[-1]
        if action == 'd':
            new_node = TreeNode()
            if not current_node.children:
                current_node.first_child = new_node
            else:
                current_node.children[-1].next_brother = new_node
            current_node.children.append(new_node)
            stack.append(new_node)
            depth = max(depth, len(stack) - 1)
        else:
            stack.pop()
    return root, depth

def height(root):
    if not root:
        return 0
    return max(height(root.first_child), height(root.next_brother)) + 1

sequence = input()
root, depth1 = BuildTree(sequence)
depth2 = height(root)
print(f"{depth1} => {depth2 - 1}")
```



代码运行截图 ==（至少包含有"Accepted"）==

20min完成

![image-20240418150936618](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240418150936618.png)



### 08581: 扩展二叉树

http://cs101.openjudge.cn/dsapre/08581/



思路：

和题解之间的差异性还是不能理解。问题不会出在中后序遍历的部分。

代码

```python
# 
# 题解标准代码
def build_tree(preorder):
    if not preorder or preorder[0] == '.':
        return None, preorder[1:]
    root = preorder[0]
    left, preorder = build_tree(preorder[1:])
    right, preorder = build_tree(preorder)
    return (root, left, right), preorder

def inorder(tree):
    if tree is None:
        return ''
    root, left, right = tree
    return inorder(left) + root + inorder(right)

def postorder(tree):
    if tree is None:
        return ''
    root, left, right = tree
    return postorder(left) + postorder(right) + root

preorder = input().strip()
tree, _ = build_tree(preorder)
print(inorder(tree))
print(postorder(tree))

# RE代码
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def BuildTree(lst):
    root = BinaryTreeNode(lst[0])
    stack = [root]
    check = 0
    for i in range(1, len(lst)):
        if check:
            check = 0
            continue
        elif i == len(lst) - 2:
            break
        elif lst[i].isupper():
            node = BinaryTreeNode(lst[i])
            while stack[-1].left and stack[-1].right:
                stack.pop()
            if stack[-1].left == None:
                stack[-1].left = node
            else:
                stack[-1].right = node
            stack.append(node)
        else:
            if lst[i + 1].isupper():
                node = BinaryTreeNode(lst[i + 1])
                stack[-1].right = node
                stack.append(node)
                check = 1
            else:
                stack.pop()
                check = 1
    return root

def midorder(root):
    if root is None:
        return []
    left = midorder(root.left) if root.left else []
    right = midorder(root.right) if root.right else []
    return left + [root.value] + right

def postorder(root):
    if root is None:
        return []
    left = postorder(root.left) if root.left else []
    right = postorder(root.right) if root.right else []
    return left + right + [root.value]

lst = list(input())
root = BuildTree(lst)
mid = midorder(root)
post = postorder(root)
print(''.join(mid))
print(''.join(post))
```



代码运行截图 ==（至少包含有"Accepted"）==

![image-20240418170022063](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240418170022063.png)

![image-20240418170001800](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240418170001800.png)



### 22067: 快速堆猪

http://cs101.openjudge.cn/practice/22067/



思路：

先尝试了暴力，然后喜提TLE

之后发现可以应用辅助栈。堆猪的时候，同时在辅助栈顶添加当前的min值，主栈pop的时候辅助栈同步pop，这样就能维持辅助栈的栈顶恒为min值。

代码

```python
# 
stack = []
weight_stack = []
while True:
    try:
        s = input()
    except EOFError:
        break
    if s == 'pop':
        if stack:
            stack.pop()
            if weight_stack:
                weight_stack.pop()
    elif s == 'min':
        if weight_stack:
            print(weight_stack[-1])
    else:
        weight = int(s.split()[1])
        stack.append(weight)
        if not weight_stack:
            weight_stack.append(weight)
        else:
            k = weight_stack[-1]
            weight_stack.append(min(k, weight))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

15min完成

![image-20240418143308839](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240418143308839.png)



### 04123: 马走日

dfs, http://cs101.openjudge.cn/practice/04123



思路：

TLE了，还有优化的空间。等考完期中考试，再来优化修改（qwq

代码

```python
# 
#pylint: skip-file
def horse(arr, finish_line=1):
    arr[0] = xx * 10 + yy
    flag = True
    if finish_line == len(arr):
        global num
        num += 1
        for i in arr:
            mm = str(int(i / 10))
            nn = str(int(i % 10))
        return 0
    for stand in arr1:
        arr[finish_line] = stand
        if finish_line >= 1:
            if abs(arr[finish_line] - arr[finish_line - 1]) != 8 and abs(
                    arr[finish_line] - arr[finish_line - 1]) != 12 and abs(
                    arr[finish_line] - arr[finish_line - 1]) != 19 and abs(
                    arr[finish_line] - arr[finish_line - 1]) != 21:
                flag = False
            else:
                flag = True
        for line in range(finish_line):
            if arr[finish_line] == arr[line]:
                flag = False
        if flag == True:
            horse(arr, finish_line + 1)

t = int(input())
for _ in range(t):
    arr1 = []
    n, m, xx, yy = map(int, input().split())
    for x in range(n):
        for y in range(m):
            a = 10 * x + y
            arr1.append(a)
    num = 0
    horse([None] * n * m)
    print(num)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240418170757432](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240418170757432.png)



### 28046: 词梯

bfs, http://cs101.openjudge.cn/practice/28046/



思路：

1、build graph：创建大量的桶，每个桶可以存放若干个单词，桶标记是去掉1个字母，通配符“_”占空的单词。所有匹配标记的单词都放到这个桶里。所有单词就位后，再在同一个桶的单词之间建立边即可。

2、bfs：采用了双端队列。BFS搜索所有从s出发，可到达的顶点的边，而且在达到更远的顶点之前，BFS会遍历完所有更近的顶点。

代码

```python
# 
from collections import deque
def construct_graph(wordlist):
    graph = {}
    for word in wordlist:
        for i in range(len(word)):
            bucket = word[:i:] + '_' + word[i+1::]
            if bucket not in graph:
                graph[bucket] = []
            graph[bucket].append(word)
    return graph

def bfs(start, end, graph):
    queue = deque([(start, [start])])
    visited = set(start)
    while queue:
        word, path = queue.popleft()
        if word == end:
            return path
        for i in range(len(word)):
            pattern = word[:i] + '_' + word[i + 1:]
            if pattern in graph:
                neighbors = graph[pattern]
                for neighbor in neighbors:
                    if neighbor not in visited:
                        visited.add(neighbor)
                        queue.append((neighbor, path + [neighbor]))
    return []

n = int(input())
wordlist = [input() for _ in range(n)]
graph = construct_graph(wordlist)
start, end = input().split()
path = bfs(start, end, graph)
if path:
    print(' '.join(path))
else:
    print('NO')
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

25min完成

![image-20240418155207442](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240418155207442.png)



### 28050: 骑士周游

dfs, http://cs101.openjudge.cn/practice/28050/



思路：



代码

```python
# 

```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

28050骑士周游

下周一考数分，周四普物，复习压力较大。因此本周作业还有一些没有深入思考的地方。考完期中之后来补。





