# Assignment #6: "树"算：Huffman,BinHeap,BST,AVL,DisjointSet

Updated 2214 GMT+8 March 24, 2024

2024 spring, Complied by ==王伟圣 数学科学学院==



**说明：**

1）这次作业内容不简单，耗时长的话直接参考题解。

2）请把每个题目解题思路（可选），源码Python, 或者C++（已经在Codeforces/Openjudge上AC），截图（包含Accepted），填写到下面作业模版中（推荐使用 typora https://typoraio.cn ，或者用word）。AC 或者没有AC，都请标上每个题目大致花费时间。

3）提交时候先提交pdf文件，再把md或者doc文件上传到右侧“作业评论”。Canvas需要有同学清晰头像、提交文件有pdf、"作业评论"区有上传的md或者doc附件。

4）如果不能在截止前提交作业，请写明原因。



**编程环境**

==（请改为同学的操作系统、编程环境等）==

操作系统：macOS Ventura 13.4.1 (c)

Python编程环境：Spyder IDE 5.2.2, PyCharm 2023.1.4 (Professional Edition)

C/C++编程环境：Mac terminal vi (version 9.0.1424), g++/gcc (Apple clang version 14.0.3, clang-1403.0.22.14.1)



## 1. 题目

### 22275: 二叉搜索树的遍历

http://cs101.openjudge.cn/practice/22275/



思路：

前序遍历：“根——左——右”，从左往右，遇到第一个大于根节点的元素时，就是左右子树的分界线。依此进行递归

代码

```python
# 
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def postorder(node):
    visit_list = [node.value]
    left = postorder(node.left) if node.left else []
    right = postorder(node.right) if node.right else []
    return left + right + visit_list

def Build_Binary_Search_Tree(preorder_list):
    if len(preorder_list) == 0:
        return None
    root = BinaryTreeNode(preorder_list[0])
    idx = len(preorder_list)
    for i in range(len(preorder_list)):
        if preorder_list[i] > preorder_list[0]:
            idx = i
            break
    root.left = Build_Binary_Search_Tree(preorder_list[1:idx:])
    root.right = Build_Binary_Search_Tree(preorder_list[idx::])
    return root

n = int(input())
preorder_list = list(map(int, input().split()))
root = Build_Binary_Search_Tree(preorder_list)
ans = postorder(root)
print(' '.join(map(str, ans)))
```



代码运行截图 ==（至少包含有"Accepted"）==

20min完成

![image-20240327103907485](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240327103907485.png)



### 05455: 二叉搜索树的层次遍历

http://cs101.openjudge.cn/practice/05455/



思路：

将某个值作为节点插入二叉搜索树node中

插入值小于根节点，则递归地考虑插入值和左子树

插入值大于根节点，则递归地考虑插入值和右子树

如果遇到（子）树根节点为空，那么就将该（子）树的根节点更新为插入值

代码

```python
# 
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def insert_node(node, value):
    if node is None:
        return BinaryTreeNode(value)
    if value < node.value:
        node.left = insert_node(node.left, value)
    elif value > node.value:
        node.right = insert_node(node.right, value)
    return node

def downorder(root):
    traversal = []
    queue = [root]
    while queue:
        node = queue.pop(0)
        traversal.append(node.value)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    return traversal

lst = list(map(int, input().split()))
root = BinaryTreeNode(lst[0])
for element in lst:
    root = insert_node(root, element)
ans = downorder(root)
print(' '.join(map(str, ans)), end="")
```



代码运行截图 ==（至少包含有"Accepted"）==

25min完成

![image-20240327111027952](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240327111027952.png)



### 04078: 实现堆结构

http://cs101.openjudge.cn/practice/04078/

练习自己写个BinHeap。当然机考时候，如果遇到这样题目，直接import heapq。手搓栈、队列、堆、AVL等，考试前需要搓个遍。



思路：

手搓（虽然中途看了几次答案）

代码

```python
# 
class BinaryHeap:
    def __init__(self):
        self.heapList = [0]
        self.currentSize = 0

    def percUp(self, i):
        while i // 2 > 0:
            if self.heapList[i] < self.heapList[i // 2]:
                tmp = self.heapList[i // 2]
                self.heapList[i // 2] = self.heapList[i]
                self.heapList[i] = tmp
            i = i // 2

    def insert(self, k):
        self.heapList.append(k)
        self.currentSize = self.currentSize + 1
        self.percUp(self.currentSize)

    def percDown(self, i):
        while (i * 2) <= self.currentSize:
            mc = self.minChild(i)
            if self.heapList[i] > self.heapList[mc]:
                tmp = self.heapList[i]
                self.heapList[i] = self.heapList[mc]
                self.heapList[mc] = tmp
            i = mc

    def minChild(self, i):
        if i * 2 + 1 > self.currentSize:
            return i * 2
        else:
            if self.heapList[i * 2] < self.heapList[i * 2 + 1]:
                return i * 2
            else:
                return i * 2 + 1

    def delMin(self):
        retval = self.heapList[1]
        self.heapList[1] = self.heapList[self.currentSize]
        self.currentSize = self.currentSize - 1
        self.heapList.pop()
        self.percDown(1)
        return retval

    def buildHeap(self, alist):
        i = len(alist) // 2
        self.currentSize = len(alist)
        self.heapList = [0] + alist[:]
        while (i > 0):
            print(f'i = {i}, {self.heapList}')
            self.percDown(i)
            i = i - 1
        print(f'i = {i}, {self.heapList}')

n = int(input())
hp = BinaryHeap()
for _ in range(n):
    s = input()
    if s[0] == '1':
        type, u = map(int, s.split())
        hp.insert(u)
    elif s == '2':
        print(hp.delMin())
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

手搓了20min

![image-20240327113534481](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240327113534481.png)



### 22161: 哈夫曼编码树

http://cs101.openjudge.cn/practice/22161/



思路：

想了很久，最终参考了题解，理解了将近2h的时间，最后磕磕绊绊的靠自己一个人敲完了代码……

代码

```python
# 
import heapq
class Node:
    def __init__(self, weight, char=None):
        self.weight = weight
        self.char = char
        self.left = None
        self.right = None

    def __lt__(self, other):
        if self.weight == other.weight:
            return self.char < other.char
        return self.weight < other.weight

def build_huffman_tree(characters):
    heap = []
    for char, weight in characters.items():
        heapq.heappush(heap, Node(weight, char))
    while len(heap) > 1:
        left = heapq.heappop(heap)
        right = heapq.heappop(heap)
        merged = Node(left.weight + right.weight, min(left.char, right.char))
        merged.left = left
        merged.right = right
        heapq.heappush(heap, merged)
    return heap[0]

def encode_huffman_tree(root):
    codes = {}
    def traverse(node, code):
        #if node.char:
        if node.left is None and node.right is None:
            codes[node.char] = code
        else:
            traverse(node.left, code + '0')
            traverse(node.right, code + '1')
    traverse(root, '')
    return codes

def huffman_encoding(codes, string):
    encoded = ''
    for char in string:
        encoded += codes[char]
    return encoded

def huffman_decoding(root, encoded_string):
    decoded = ''
    node = root
    for bit in encoded_string:
        if bit == '0':
            node = node.left
        else:
            node = node.right
        if node.left is None and node.right is None:
            decoded += node.char
            node = root
    return decoded

n = int(input())
characters = {}
for _ in range(n):
    char, weight = input().split()
    characters[char] = int(weight)
huffman_tree = build_huffman_tree(characters)
codes = encode_huffman_tree(huffman_tree)
strings = []
while True:
    try:
        line = input()
        strings.append(line)
    except EOFError:
        break
results = []
for string in strings:
    if string[0] in ('0','1'):
        results.append(huffman_decoding(huffman_tree, string))
    else:
        results.append(huffman_encoding(codes, string))
for result in results:
    print(result)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240327115313923](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240327115313923.png)



### 晴问9.5: 平衡二叉树的建立

https://sunnywhy.com/sfbj/9/5/359



思路：

重点在于左旋、右旋、insert操作的实现

代码

```python
# 
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None
        self.height = 1

class AVL:
    def __init__(self):
        self.root = None

    def insert(self, value):
        if not self.root:
            self.root = BinaryTreeNode(value)
        else:
            self.root = self._insert(value, self.root)

    def _insert(self, value, node):
        if not node:
            return BinaryTreeNode(value)
        elif value < node.value:
            node.left = self._insert(value, node.left)
        else:
            node.right = self._insert(value, node.right)

        node.height = 1 + max(self._get_height(node.left), self._get_height(node.right))

        balance = self._get_balance(node)

        if balance > 1:
            if value < node.left.value:
                return self._rotate_right(node)
            else:
                node.left = self._rotate_left(node.left)
                return self._rotate_right(node)

        if balance < -1:
            if value > node.right.value:
                return self._rotate_left(node)
            else:
                node.right = self._rotate_right(node.right)
                return self._rotate_left(node)

        return node

    def _get_height(self, node):
        if not node:
            return 0
        return node.height

    def _get_balance(self, node):
        if not node:
            return 0
        return self._get_height(node.left) - self._get_height(node.right)

    def _rotate_left(self, z):
        y = z.right
        T2 = y.left
        y.left = z
        z.right = T2
        z.height = 1 + max(self._get_height(z.left), self._get_height(z.right))
        y.height = 1 + max(self._get_height(y.left), self._get_height(y.right))
        return y

    def _rotate_right(self, y):
        x = y.left
        T2 = x.right
        x.right = y
        y.left = T2
        y.height = 1 + max(self._get_height(y.left), self._get_height(y.right))
        x.height = 1 + max(self._get_height(x.left), self._get_height(x.right))
        return x

    def preorder(self):
        return self._preorder(self.root)

    def _preorder(self, node):
        if not node:
            return []
        return [node.value] + self._preorder(node.left) + self._preorder(node.right)

n = int(input())
lst = list(map(int, input().split()))
avl = AVL()
for value in lst:
    avl.insert(value)
print(' '.join(map(str, avl.preorder())))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

1h手搓

![image-20240327133606174](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240327133606174.png)

### 02524: 宗教信仰

http://cs101.openjudge.cn/practice/02524/



思路：

创建并查集：

如果i和j宗教信仰相同，那么就把i和j所在的集合合并为一个集合，最终count并查集内互不相交的集合数目即可

代码

```python
# 
class Disjointset:
    def __init__(self, n):
        self.rank = [1] * n
        self.parent = [i for i in range(n)]

    def find(self, x):
        if (self.parent[x] != x):
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        xset = self.find(x)
        yset = self.find(y)
        if xset == yset:
            return
        if self.rank[xset] < self.rank[yset]:
            self.parent[xset] = yset
        elif self.rank[xset] > self.rank[yset]:
            self.parent[yset] = xset
        else:
            self.parent[yset] = xset
            self.rank[xset] = self.rank[xset] + 1
case = 0
while True:
    case += 1
    n, m = map(int, input().split())
    disjointset = Disjointset(n)
    if n == 0 and m == 0:
        break
    for _ in range(m):
        i, j = map(int, input().split())
        disjointset.union(i - 1, j - 1)
    belief_classes = [ x for x in range(n) if x == disjointset.parent[x]]
    print(f'Case {case}: {len(belief_classes)}')
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

30min完成

![image-20240327125108844](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240327125108844.png)



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

作业题目好难啊……

代码量提高带来的更高细节要求、手搓堆+AVL树+并查集的熟练度不够，导致本次作业对题解的依赖度陡然上升。

决定先放下每日选做，重新回看一遍md讲义，看过题解的题目隔段时间重新做一次。争取4月月考能不靠cheatsheetAC3-4个题。



