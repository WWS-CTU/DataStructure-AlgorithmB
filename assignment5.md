# Assignment #5: "树"算：概念、表示、解析、遍历

Updated 2124 GMT+8 March 17, 2024

2024 spring, Complied by ==王伟圣 数学科学学院==



**说明：**

1）The complete process to learn DSA from scratch can be broken into 4 parts:

Learn about Time complexities, learn the basics of individual Data Structures, learn the basics of Algorithms, and practice Problems.

2）请把每个题目解题思路（可选），源码Python, 或者C++（已经在Codeforces/Openjudge上AC），截图（包含Accepted），填写到下面作业模版中（推荐使用 typora https://typoraio.cn ，或者用word）。AC 或者没有AC，都请标上每个题目大致花费时间。

3）提交时候先提交pdf文件，再把md或者doc文件上传到右侧“作业评论”。Canvas需要有同学清晰头像、提交文件有pdf、"作业评论"区有上传的md或者doc附件。

4）如果不能在截止前提交作业，请写明原因。



**编程环境**

==（请改为同学的操作系统、编程环境等）==

操作系统：macOS Ventura 13.4.1 (c)

Python编程环境：Spyder IDE 5.2.2, PyCharm 2023.1.4 (Professional Edition)

C/C++编程环境：Mac terminal vi (version 9.0.1424), g++/gcc (Apple clang version 14.0.3, clang-1403.0.22.14.1)



## 1. 题目

### 27638: 求二叉树的高度和叶子数目

http://cs101.openjudge.cn/practice/27638/



思路：

主要关注找根节点的过程



代码

```python
# 
class BinaryTreeNode:
    def __init__(self):
        self.left = None
        self.right = None

def height(node):
    if node is None:
        return -1
    return max(height(node.left), height(node.right)) + 1

def count_leaves(node):
    if node is None:
        return 0
    if node.left == None and node.right == None:
        return 1
    left = count_leaves(node.left)
    right = count_leaves(node.right)
    return left + right

n = int(input())
nodes = [BinaryTreeNode() for _ in range(n)]
has_parent = [False] * n
for i in range(n):
    a, b = map(int, input().split())
    if a != -1:
        nodes[i].left = nodes[a]
        has_parent[a] = True
    if b != -1:
        nodes[i].right = nodes[b]
        has_parent[b] = True

root = nodes[has_parent.index(False)]
print(height(root), count_leaves(root))
```



代码运行截图 ==（至少包含有"Accepted"）==

10min完成

![image-20240318184850982](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240318184850982.png)

### 24729: 括号嵌套树

http://cs101.openjudge.cn/practice/24729/



思路：

1、用栈解析树

2、完成前序+后序遍历



代码

```python
# 
class Stack:
    def __init__(self):
        self.items = []
    def push(self, element):
        self.items.append(element)

    def pop(self):
        if len(self.items) == 0:
            return None
        else:
            return self.items.pop()
    def is_empty(self):
        return len(self.items) == 0

    def get_top(self):
        top = self.pop()
        self.push(top)
        return top

class TreeNode:
    def __init__(self, value):
        self.value = value
        self.children = []

def preorder(node):
    visit_list = [node.value]
    for child in node.children:
        visit_list.extend(preorder(child))
    return ''.join(visit_list)

def postorder(node):
    visit_list = []
    for child in node.children:
        visit_list.extend(postorder(child))
    visit_list.append(node.value)
    return ''.join(visit_list)

s = input().strip()
s = ''.join(s.split())
node = None
stack = Stack()
for i in range(len(s)):
    if s[i] not in '(),':
        node = TreeNode(s[i])
        if not stack.is_empty():
            stack.get_top().children.append(node)
    if s[i] == '(':
        if node:
            stack.push(node)
            node = None
    if s[i] == ')':
        node = stack.pop()

print(preorder(node))
print(postorder(node))
```



代码运行截图 ==（至少包含有"Accepted"）==

20min完成

![image-20240318192328910](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240318192328910.png)



### 02775: 文件结构“图”

http://cs101.openjudge.cn/practice/02775/



思路：

qwq

第一次做的时候忘记重置root了导致WA

代码

```python
# 
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.dirs = []
        self.files = []

def myprint(node, idnum = 0):
    fixed = "|     " * idnum
    print(fixed + node.value)
    for dir in node.dirs:
        myprint(dir, idnum + 1)
    for file in sorted(node.files):
        print(fixed + file)

num = 1
dataset, tmp = [], []
while True:
    s = input()
    if s == '*':
        dataset.append(tmp)
        tmp = []
    elif s == '#':
        break
    else:
        tmp.append(s)

for data in dataset:
    print(f'DATA SET {num}:')
    root = TreeNode('ROOT')
    num += 1
    stack = [root]
    for s in data:
        if s[0] == 'f':
            stack[-1].files.append(s)
        if s[0] == 'd':
            dir = TreeNode(s)
            stack[-1].dirs.append(dir)
            stack.append(dir)
        if s == ']':
            stack.pop()
    myprint(root)
    if num <= len(dataset):
        print()
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

25min完成

![image-20240318195411699](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240318195411699.png)



### 25140: 根据后序表达式建立队列表达式

http://cs101.openjudge.cn/practice/25140/



思路：

对每个字符，先创建一个无左右儿子的节点

小写字母：节点压入栈

大写字母：先pop栈顶作为右儿子，再pop栈顶作为左儿子，最后把当前节点压入栈中

最后栈中只会有一个元素，它就是root节点



层次遍历代码直接记忆吧~

代码

```python
# 
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def downorder(root):  ##层次遍历
    queue = [root]
    traversal = []
    while queue:
        node = queue.pop(0)
        traversal.append(node.value)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    return traversal

n = int(input())
for _ in range(n):
    s = input()
    stack = []
    for char in s:
        if char.islower():
            stack.append(BinaryTreeNode(char))
        if char.isupper():
            node = BinaryTreeNode(char)
            node.right = stack.pop()
            node.left = stack.pop()
            stack.append(node)
    root = stack[-1]
    ans = downorder(root)[::-1]
    print(''.join(ans))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

20min完成

![image-20240318202635889](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240318202635889.png)

### 24750: 根据二叉树中后序序列建树

http://cs101.openjudge.cn/practice/24750/



思路：

后序遍历的最后一个是根节点，据此将中序遍历划分为左右两棵子树，递归地遍历

代码

```python
# 
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def build_tree(mid, post):
    if not mid or not post:
        return None
    root_val = post.pop()
    root = BinaryTreeNode(root_val)
    root_id = mid.index(root_val)
    root.right = build_tree(mid[root_id + 1::], post)
    root.left = build_tree(mid[:root_id:], post)
    return root

def preorder(root):
    if root:
        visit_list = [root.value]
        visit_list.extend(preorder(root.left))
        visit_list.extend(preorder(root.right))
        return ''.join(visit_list)
    return []

mid = input()
post = input()
root = build_tree(list(mid), list(post))
print(preorder(root))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

20min完成

![image-20240318210138674](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240318210138674.png)

### 22158: 根据二叉树前中序序列建树

http://cs101.openjudge.cn/practice/22158/



思路：

与上题类似

代码

```python
# 
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def build_tree(pre, mid):
    if not pre or not mid:
        return None
    root_val = pre[0]
    root = BinaryTreeNode(root_val)
    root_id = mid.index(root_val)
    root.left = build_tree(pre[1:root_id + 1:], mid[:root_id:])
    root.right = build_tree(pre[root_id + 1::], mid[root_id + 1::])
    return root

def postorder(root):
    visit_list = []
    if root:
        visit_list.extend(postorder(root.left))
        visit_list.extend(postorder(root.right))
        visit_list.append(root.value)
        return ''.join(visit_list)
    return []

while True:
    try:
        pre = list(input())
        mid = list(input())
        root = build_tree(pre, mid)
        print(postorder(root))
    except EOFError:
        break
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

10min完成

![image-20240318211521908](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240318211521908.png)



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

这次作业主要是练习树的基本操作：3种遍历方式，寻找根节点，求高度和叶子数目

暴露出的问题有：变量命名不规范导致头昏脑胀；写函数体的时候，随着函数变得复杂，忘记了函数返回值的类型，出现多次'BinaryTreeNode' object is not iterable，’NoneType‘ object is not iterable之类的错误。

总之，树还需要继续加强，先尽力跟上每日选做



