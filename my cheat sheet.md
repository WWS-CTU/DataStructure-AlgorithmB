# cheat sheet

第一行加`# pylint: skip-file`可以忽略检查

`RuntimeError`考虑使用`sys.setrecrusionlimit(layor)`避免爆栈

小技巧：

```python
#
from operator import itemgetter, add
from functools import reduce
from collections import deque, Counter
from itertools import product

str.lstrip() / str.rstrip(): 移除字符串左侧/右侧的空白字符。
str.find(sub): 返回子字符串`sub`在字符串中首次出现的索引，如果未找到，则返回-1。
str.replace(old, new): 将字符串中的`old`子字符串替换为`new`。
str.startswith(prefix) / str.endswith(suffix): 检查字符串是否以`prefix`开头或以`suffix`结尾。
str.isalpha() / str.isdigit() / str.isalnum(): 检查字符串是否全部由字母/数字/字母和数字组成。
str.title()：每个单词首字母大写。

## itemgetter函数用法：排序
lst = [(1, 2), (2, 1), (0, 3)]
print(lst.sort(key = itemgetter(1, 0))) ## 先按照第1个指标，再按照第0个指标，均升序（sort升序，降序reverse=True）

## add和reduce用法：合并列表
reduce(add, (lst1, lst2, lst3))

## deque：双向队列；deque的pop和append的复杂度是O(1)，比list.pop(0)的时间复杂度O(n)好
q = deque() ## 创建空的双向队列
q = deque([1,2,3])
q = deque('123') ## 元素是str

## 以下以q = deque([1,2,3])为例
q.appendleft(0) ## 队头添加元素
q.append(0) ## 队尾添加元素
q.insert(2, 7) ## 现在q = deque([1,2,7,3])
q.popleft() ## 返回队头元素并将其弹出
q.pop() ## 返回队尾元素并将其弹出
q.remove(2) ## 删除指定元素
q.extendleft(sequence) ## 队头添加可迭代对象
q.extend(sequence) ## 队尾添加可迭代对象
q.append(q.popleft()) ## deque可以同时操作左右两端，此时q = deque([2,3,1])
q.rotate(2) ## 此时q = deque([2,3,1])
## 限制长度的deque增加元素
q = deque([1,2,3,4], maxlen = 4)
q.append(0) ## 此时q = deque([2,3,4,0], maxlen = 4)

# 创建一个Counter对象
count = Counter(['apple', 'banana', 'apple', 'orange', 'banana', 'apple'])
# 输出Counter对象
print(count)  # 输出: Counter({'apple': 3, 'banana': 2, 'orange': 1})
# 访问单个元素的计数
print(count['apple'])  # 输出: 3
# 访问不存在的元素返回0
print(count['grape'])  # 输出: 0
# 添加元素
count.update(['grape', 'apple'])
print(count)  # 输出: Counter({'apple': 4, 'banana': 2, 'orange': 1, 'grape': 1})

##虽然 product(A,B) 和 [(x, y) for x in A for y in B]意义一样，但是却有着不同的复杂度。product省空间费时间；列表生成式省时间费空间。
```

### 单调栈

**单调递增栈**：只有比栈顶元素小的元素才能直接进栈，否则需要先将栈中比当前元素小的元素出栈，再将当前元素入栈。这样就保证了：栈中保留的都是比当前入栈元素大的值，并且从**栈顶到栈底的元素值是单调递增的**。

寻找**左侧第一个**比当前元素**大**的元素：

**从左到右**遍历元素，构造单调递增栈（从栈顶到栈底递增）： 一个元素左侧第一个比它大的元素就是将其「插入单调递增栈」时的栈顶元素。 如果插入时的栈为空，则说明左侧不存在比当前元素大的元素。

寻找**右侧第一个**比当前元素**大**的元素：

**从左到右**遍历元素，构造单调递增栈（从栈顶到栈底递增）： 一个元素右侧第一个比它大的元素就是将其「弹出单调递增栈」时即将插入的元素。 如果该元素没有被弹出栈，则说明右侧不存在比当前元素大的元素。

```python
#找出一些连续的奶牛，要求最左边的奶牛A是最矮的，最右边的B是最高的，且B高于A。中间如果存在奶牛，则身高不能和A,B奶牛相同。问这样的奶牛最多会有多少头？
stack = []
n = int(input())
h = [int(input()) for _ in range(n)]
left, right = [0]*n, [0]*n

for i in range(n):
    while stack and h[stack[-1]] >= h[i]:
        right[stack.pop()] = i
    stack.append(i)
while stack:
    right[stack.pop()] = n

for i in range(n - 1, -1, -1):
    while stack and h[stack[-1]] <= h[i]:
        left[stack.pop()] = i
    stack.append(i)
while stack:
    left[stack.pop()] = -1

ans = 0
for i in range(n):
     if i - left[i] <= ans:
        continue
    for j in range(left[i] + 1, i):
        if right[j] > i:
            ans = max(ans, i - j + 1)
print(ans)
```

#### 前序表达式 (波兰表达式) 求值

##### 递归

递归定义波兰表达式, 再用递归求解: **波兰表达式是一个数, 或者“操作符 波兰表达式 波兰表达式”**, 这是一个 **top-down** 的过程. 

##### 栈

从后往前扫表达式, 遇到数字压入栈, 遇到操作符从栈中弹出两个操作数 (注意顺序) 做计算, 运算完再压入栈, 扫完一遍表达式刚好运算完, 栈内唯一元素即运算结果；这是一个 **bottom-up** 的过程. 

注意, 如果从前往后看, 遇见操作符压入栈等待操作数出现再运算, 运算后再压入栈, 则扫一遍表达式无法完成所有计算, 事实上只计算了一层；这个思路也是一个 **top-down** 的过程, 但没有算完, 可以再用递归算完.

```python
stack = []
s = list(input().split())
s.reverse()
for t in s:
    if t in '+-*/':
        a, b = stack.pop(), stack.pop()
        stack.append(str(eval(a+t+b)))
    else:
        stack.append(t)
print(eval(stack[0]))
```

#### 后序表达式 (逆波兰表达式) 求值

##### 递归

递归定义逆波兰表达式, 再用递归求解: **逆波兰表达式是一个数, 或者“逆波兰表达式 逆波兰表达式 操作符”**；递归永远是一个 **top-down** 的过程, 所以结合波兰表达式两种做法的经验 (递归法从左向右看, 化整为零；栈法从后往前看, 化零为整) , 逆波兰表达式要用递归法的话需要从后往前看, 自顶向下、化整为零. 

##### 栈

从前往后扫表达式, 遇到数字压入栈, 遇到操作符从从栈中弹出两个操作数 (注意顺序) 做计算, 运算完再压入栈, 扫完一遍表达式刚好运算完, 栈内唯一元素即运算结果；这是一个 **bottom-up** 的过程. 

可以看出, 这和前序表达式的计算是非常相似的, 主要区别在于扫的方向和弹出操作数后两个操作数的顺序. 

```python
stack = []
s = list(input().split())
for t in s:
    if t in '+-*/':
        b, a = stack.pop(), stack.pop()
        stack.append(str(eval(a+t+b)))
    else:
        stack.append(t)
print(eval(stack[0]))
```

#### 中序表达式(正常的四则运算表达式)转后序表达式——shunting yard算法

```python
for _ in range(int(input())):#此法对小数也适用
    s = input().strip()
    ans = []
    op = []
    operators = {'+': 1, '-': 1, '*': 2, '/': 2}
    dic = {i: True for i in '0123456789.'}
    idx = 0
    n = len(s)
    while idx < n:
        if s[idx] in dic:
            i = idx
            while i < n and s[i] in dic:
                i += 1
            ans.append(s[idx: i])
            idx = i - 1
        elif s[idx] == '(':
            op.append('(')
        elif s[idx] == ')':
            while op[-1] != '(':
                ans.append(op.pop())
            op.pop()
        else:
            while op and op[-1] != '(' and operators[op[-1]] >= operators[s[idx]]:
                ans.append(op.pop())
            op.append(s[idx])
        idx += 1
    if op:
        op.reverse()
        print(f'{" ".join(ans)} {" ".join(op)}')
    else:
        print(" ".join(ans))
```

注：输入数据格式处理

```python
stack = []
inp = list(input().split())
s = []
for word in inp:
    if '+' not in word and '-' not in word and '*' not in word and '/' not in word:
        s.append(eval(word))
    else:
        s.append(word)
```

**检测括号嵌套**：

请判断字符串的括号是否都正确配对以及有无括号嵌套。

括号交叉算不正确配对，例如"1234[78)ab]"就不算正确配对。

一对括号被包含在另一对括号里面，例如"12(ab[8])"就算括号嵌套。括号嵌套不影响配对的正确性。 

给定一个字符串: 如果括号没有正确配对，则输出 "ERROR"； 如果正确配对了，且有括号嵌套现象，则输出"YES" ；如果正确配对了，但是没有括号嵌套现象，则输出"NO"

```python
def check_brackets(s):
    stack = []
    nested = False
    pairs = {')': '(', ']': '[', '}': '{'}
    for ch in s:
        if ch in pairs.values():
            stack.append(ch)
        elif ch in pairs.keys():
            if not stack or stack.pop() != pairs[ch]:
                return "ERROR"
            if stack:
                nested = True
    if stack:
        return "ERROR"
    return "YES" if nested else "NO"
s = input()
print(check_brackets(s))
```

## 1. 排序

### 归并排序+逆序对计数

1、归并排序的步骤：

（1）分解：将待排序的数组不断分成两个子数组，直到每个子数组只有一个元素为止。

（2）合并：将相邻的两个子数组合并成一个有序数组，直到最后只剩下一个有序数组为止。

合并的过程中，需要用到一个辅助数组来暂存合并后的有序数组。具体来说，假设待合并的两个有序数组分别为 A 和 B，它们的长度分别为 n 和 m，合并后的有序数组为tmp，那么合并的过程可以按如下步骤进行：

①定义三个指针 i、j 和 k，分别指向数组 A、B 和 tmp 的起始位置。

②比较 A[i] 和 B[j] 的大小，将小的元素放入 tmp[k] 中，并将对应指针（A[i]>B[j] 则 j++）向后移动一位。

③重复步骤 2，直到其中一个数组的元素全部放入tmp中。

④将另一个数组的元素放入tmp中

时间复杂度：nlogn

```python
#
def merge(left, right):
    i = j = inv = 0
    tmp = [] #tmp储存合并后的数组（升序）
    while i < len(left) and j < len(right):
        if left[i] > right[j]: #当前左数组元素大于右数组元素：tmp放入右数组元素
            tmp.append(right[j])
            j += 1
            inv += len(left) - i #左数组第i个元素之后的每个元素都大于left[i]（因为此时左数组已经升序）
        elif left[i] < right[j]:
            tmp.append(left[i])
            i += 1
    tmp += left[i::]
    tmp += right[j::] #tmp补上未参与排序的部分
    return tmp, inv

def count_inverse(lst): #逆序对计数
    if len(lst) < 2:
        return (lst, 0)
    mid = len(lst) // 2
    left, inv1 = count_inverse(lst[:mid:]) #左数组逆序对
    right, inv2 = count_inverse(lst[mid::]) #右数组逆序对
    merged, inv = merge(left, right) #对左右数组归并排序，计算逆序对
    inv += inv1 + inv2
    return merged, inv

#逆序对另一种算法
from bisect import *
a=[]
rev=0
for _ in range(n):
    num=int(input())
    rev+=bisect_left(a,num)
    insort_left(a,num)
ans=n*(n-1)//2-rev
```

## 2.树

### 二叉树操作

```python
#
class BinaryTreeNode:
    def __init__(self, value = None):
        self.value = value
        self.left = None
        self.right = None

def height(node):   ##二叉树高度
    if node is None:
        return -1
    return max(height(node.left), height(node.right)) + 1

def count_leaves(node):  ##二叉树叶子节点数目
    if node is None:
        return 0
    if node.left == None and node.right == None:
        return 1
    left = count_leaves(node.left)
    right = count_leaves(node.right)
    return left + right

## 二叉树层次遍历
def downorder(root):
    queue = [root]
    traversal = []
    while queue:
        node = queue.pop(0)  ## pop(0)表示弹出列表第一个元素
        traversal.append(node.value)
        if node.left:
            queue.append(node.left)
        if node.right:
            queue.append(node.right)
    return traversal

## 根据中后序表达式建二叉树
def BuildTree_ByMidAndPost(mid, post):  ##mid和post均为list
    if not mid or not post:
        return None
    root_val = post.pop()
    root_index = mid.index(root_val)
    root = BinaryTreeNode(root_val)
    root.right = BuildTree_ByMidAndPost(mid[root_index + 1::], post)  ##必须先走right再走left，post不能换成切片
    root.left = BuildTree_ByMidAndPost(mid[:root_index:], post)
    return root

##  根据前中序表达式建二叉树
def BuildTree_ByPreAndMid(pre, mid):  ##pre和mid均为list
    if not pre or not mid:
        return None
    root_val = pre[0]
    root = BinaryTreeNode(root_val)
    root_id = mid.index(root_val)
    root.left = BuildTree_ByPreAndMid(pre[1:root_id + 1:], mid[:root_id:])
    root.right = BuildTree_ByPreAndMid(pre[root_id + 1::], mid[root_id + 1::])
    return root

## 二叉树前序遍历
def preorder(root):
    visit_list = [root.value]
    left = preorder(root.left) if root.left else []
    right = preorder(root.right) if root.right else []
    return visit_list + left + right

## 二叉树中序遍历
def midorder(root):
    visit_list = [root.value]
    left = midorder(root.left) if root.left else []
    right = midorder(root.right) if root.right else []
    return left + visit_list + right

## 二叉树后序遍历
def postorder(root):
    visit_list = [root.value]
    left = postorder(root.left) if root.left else []
    right = postorder(root.right) if root.right else []
    return left + right + visit_list
```

### 二叉搜索树：

小于父节点的节点都在左子树中，大于父节点的节点则都在右子树中。

**遍历性质：**

1、前序遍历：“根——左——右”，从左往右，遇到第一个大于根节点的元素时，就是左右子树的分界线。依此进行递归

2、中序遍历：就是所有节点值的升序排列

3、后序遍历：“左——右——根”，从右往左，遇到第一个小于根节点的元素时，就是左右子树的分界线。依此进行递归

```python
#
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

## 将某个值作为节点插入二叉搜索树node中
## 插入值小于根节点，则递归地考虑插入值和左子树
## 插入值大于根节点，则递归地考虑插入值和右子树
## 如果遇到（子）树根节点为空，那么就将该（子）树的根节点更新为插入值
def insert_node(node, value):
    if node is None:
        return BinaryTreeNode(value)
    if value < node.value:
        node.left = insert_node(node.left, value)
    elif value > node.value:
        node.right = insert_node(node.right, value)
    return node

## 根据前序遍历构建二叉搜索树
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
```

### 树：

**前序遍历** 在前序遍历中，先访问根节点，然后递归地前序遍历左子树，最后递归地前序遍历右子树。

**中序遍历** 在中序遍历中，先递归地中序遍历左子树，然后访问根节点，最后递归地中序遍历右子树。

**后序遍历** 在后序遍历中，先递归地后序遍历左子树，然后递归地后序遍历右子树，最后访问根节点。

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

class TreeNode:  ## 列表表示一般的树节点
    def __init__(self, value):
        self.value = value
        self.children = []

def parse_tree(s): ##解析树（括号嵌套输入）
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
            
## 对一个已经按照列表嵌套形式表示好的树进行遍历

## 前序遍历
def preorder(node):
    visit_list = [node.value]
    for child in node.children:
        visit_list.extend(preorder(child))
    return ''.join(visit_list)

## 后序遍历
def postorder(node):
    visit_list = []
    for child in node.children:
        visit_list.extend(postorder(child))
    visit_list.append(node.value)
    return ''.join(visit_list)

## 层次遍历
def downorder(root):
    queue = [root]
    traversal = []
    while queue:
        node = queue.pop(0)
        traversal.append(node.value)
        for child in node.children:
            if child:
                queue.append(child)
    return traversal
```

**左孩子右兄弟**表示任意一颗一般的树：左孩子右兄弟法的二叉树，二叉树节点的左子节点是原树该节点的第一个孩子节点，右子节点是与左孩子相邻的一个兄弟节点。

![img](https://img-blog.csdnimg.cn/9ad4401df38340b7ab92f7f51802f021.png)

```python
class Node: 
	def __init__(self, data): 
		self.data = data
		self.firstChild = None
		self.nextSibling = None
class BinaryTreeNode:
    def __init__(self, value = None):
        self.value = value
        self.left = None
        self.right = None
def convert(root: Node) -> BinaryTreeNode: ##存储了nextSibling的时候的建树方式
	"""将以root为根的多叉树转换为二叉树, 并返回该二叉树的根节点"""
	# 两个if导致 base case 省略
    # step 1: build root
    new_root = BinaryTreeNode()
    # step 2: connect subtrees
    if root.children:  
        new_root.left = convert(root.children[0])  
    if root.next_sibling:  
        new_root.right = convert(root.nextSibling)  
    return new_root
## 没有存next_sibling也可以转二叉树, 但需要换个角度来建这棵二叉树, 即不是按照“建根, 连左子树, 连右子树”的方式进行, 而是按原多叉树的特点进行: 
class TreeNode:
    def __init__(self, value = None):
        self.value = value
        self.children = []
def convert(root: TreeNode) -> BinaryTreeNode:
	"""将以root为根的多叉树转换为二叉树, 并返回该二叉树的根节点"""
	# base case
    if not root:  
        return None  
	# step 1: 建根
    binary_root = BinaryTreeNode()  
    # step 2: 将原多叉树的第一个子树 (转换出的二叉树) 连接为二叉树的左子树
    if root.children:  
        binary_root.left = convert(root.children[0])  
	# step 3: 将原多叉树的其他子树 (转换出的二叉树) , 按顺序 (类似链表) 连接为前一棵子树 (转换出的二叉树) 的右子树
    curr = binary_root.left  
    for i in range(1, len(root.children)):  
        curr.right = convert(root.children[i])  
        curr = curr.right  
	# step 4: return
    return binary_root
```

### 二叉堆：

二叉堆本质上是一种完全二叉树，它分为两类：**最大堆和最小堆**。最大堆的任何一个父节点的值都大于或等于它左右孩子节点的值；最小堆的任何一个父节点的值，都小于或等于它左右孩子节点的值。

```python
## 手搓二叉堆（最小堆）
class BinaryHeap:  ## 创建数据类型：二叉堆
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

    def insert(self, k):  ## 向二叉堆中添加新元素
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

    def delMin(self):  ## 删除二叉堆中最小元素
        retval = self.heapList[1]
        self.heapList[1] = self.heapList[self.currentSize]
        self.currentSize = self.currentSize - 1
        self.heapList.pop()
        self.percDown(1)
        return retval

    def buildHeap(self, alist):  ## 根据元素列表构建二叉堆
        i = len(alist) // 2
        self.currentSize = len(alist)
        self.heapList = [0] + alist[:]
        while (i > 0):
            print(f'i = {i}, {self.heapList}')
            self.percDown(i)
            i = i - 1
        print(f'i = {i}, {self.heapList}')
```

python中的库heapq提供了最小二叉堆的相关操作：

```python
#
import heapq 
## heapq是默认构造小顶堆
nums = [4, 3, 2, 1]
hp = []
for num in nums:
	#构建小顶堆hp，并插入元素
    heapq.heappush(hp, num)
print(hp)  # [1, 2, 3, 4]

#直接构建小顶堆,并转换成列表
nums = [4, 3, 2, 1]
heapq.heapify(nums)
print(nums) # [1, 3, 2, 4]

#弹出堆顶元素，重新调整堆
nums = [4, 3, 2, 1]
heapq.heapify(nums)
heapq.heappop(nums)
print(nums) # [2, 3, 4]

#弹出最小元素，并插入元素调整堆的位置
nums = [4, 3, 2, 1]
heapq.heapify(nums)
heapq.heapreplace(nums, 5)
print(nums) # [2, 3, 5, 4]

#返回出前K个最大元素
nums = [4, 3, 2, 1]
heapq.heapify(nums)
k = heapq.nlargest(2, nums)
print(k) # [4, 3]

#返回前K个最小元素
nums = [4, 3, 2, 1]
heapq.heapify(nums)
k = heapq.nsmallest(2, nums)
print(k) # [1, 2]
```

### 并查集

在并查集中，每个元素都属于一个集合，并且这些集合之间是不相交的。为了高效地实现并查集操作，通常会使用树形结构来表示集合之间的关系。每个集合可以用一个树表示，其中树的根节点是集合的代表元素。

```python
#
class Disjointset:
	def __init__(self, n): ## 创建并查集，元素从0到n-1
		self.rank = [1] * n
		self.parent = [i for i in range(n)]
	# 找到元素x所在的集合
	def find(self, x):
		if (self.parent[x] != x):
			self.parent[x] = self.find(self.parent[x])
		return self.parent[x]
	# 合并x和y所在的集合
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
    # 统计并查集内不相交的集合数目
    def count(self):
        return len([ x for x in range(n) if x == self.parent[x] ])
```

### AVL树（平衡二叉搜索树）

平衡因子：左右子树的高度之差

我们认为，保证树的平衡因子为-1、0或1，可以使关键操作获得更好的大 O 性能。首先考虑平衡因子如何改善最坏情况。有左倾与右倾这两种可能性。

```Python
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

    # 把value插入AVL树
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
            if value < node.left.value:	# 树形是 LL
                return self._rotate_right(node)
            else:	# 树形是 LR
                node.left = self._rotate_left(node.left)
                return self._rotate_right(node)

        if balance < -1:
            if value > node.right.value:	# 树形是 RR
                return self._rotate_left(node)
            else:	# 树形是 RL
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

    # 左旋
    def _rotate_left(self, z):
        y = z.right
        T2 = y.left
        y.left = z
        z.right = T2
        z.height = 1 + max(self._get_height(z.left), self._get_height(z.right))
        y.height = 1 + max(self._get_height(y.left), self._get_height(y.right))
        return y

    # 右旋
    def _rotate_right(self, y):
        x = y.left
        T2 = x.right
        x.right = y
        y.left = T2
        y.height = 1 + max(self._get_height(y.left), self._get_height(y.right))
        x.height = 1 + max(self._get_height(x.left), self._get_height(x.right))
        return x

    # AVL树的前序遍历
    def preorder(self):
        return self._preorder(self.root)

    def _preorder(self, node):
        if not node:
            return []
        return [node.value] + self._preorder(node.left) + self._preorder(node.right)
    
    # AVL树的后序遍历
    def postorder(self):
        return self._postorder(self.root)

    def _postorder(self, node):
        if not node:
            return []
        return self._postorder(node.left) + self._postorder(node.right) + [node.value]
    
    # AVL树的中序遍历和二叉搜索树一样，就是所有节点值的升序排列
```

**Huffman树，Huffman算法**

带权路径长度（WPL）最小的二叉树称作哈夫曼树。

这段代码首先定义了一个 `Node` 类来表示哈夫曼树的节点。然后，使用最小堆来构建哈夫曼树，每次从堆中取出两个频率最小的节点进行合并，直到堆中只剩下一个节点，即哈夫曼树的根节点。接着，使用递归方法计算哈夫曼树的带权外部路径长度（weighted external path length）。最后，输出计算得到的带权外部路径长度。

```python
import heapq
class Node:
    def __init__(self, char, freq):
        self.char = char
        self.freq = freq
        self.left = None
        self.right = None
    def __lt__(self, other):
        return self.freq < other.freq

def huffman_encoding(char_freq):
    heap = [Node(char, freq) for char, freq in char_freq.items()]
    heapq.heapify(heap)
    while len(heap) > 1:
        left = heapq.heappop(heap)
        right = heapq.heappop(heap)
        merged = Node(None, left.freq + right.freq) # note: 合并之后 char 字典是空
        merged.left = left
        merged.right = right
        heapq.heappush(heap, merged)
    return heap[0]

def external_path_length(node, depth=0):
    if node is None:
        return 0
    if node.left is None and node.right is None:
        return depth * node.freq
    return (external_path_length(node.left, depth + 1) +
            external_path_length(node.right, depth + 1))

char_freq = {'a': 3, 'b': 4, 'c': 5, 'd': 6, 'e': 8, 'f': 9, 'g': 11, 'h': 12}
huffman_tree = huffman_encoding(char_freq)
external_length = external_path_length(huffman_tree)
print("The weighted external path length of the Huffman tree is:", external_length)

# Output:
# The weighted external path length of the Huffman tree is: 169 
```

### 图

表示方式：**dict的value如果是list/set，是邻接表。dici嵌套dict 是 字典树/前缀树/Trie**

```python
# 邻接表（表示有向图）
graph = {
    'A': ['B', 'C'],
    'B': ['A', 'D'],
    'C': ['A', 'D'],
    'D': ['B', 'C']
}
# 字典树（前缀树，Trie）：字典树是一种树形数据结构，用于高效地存储和检索字符串数据集中的键。如果你使用嵌套的字典来表示字典树，其中每个字典代表一个节点，键表示路径上的字符，而值表示子节点，那么就构成了字典树。例如：
trie = {
    'a': {
        'p': {
            'p': {
                'l': {
                    'e': {'is_end': True}
                }
            }
        }
    },
    'b': {
        'a': {
            'l': {
                'l': {'is_end': True}
            }
        }
    },
    'c': {
        'a': {
            't': {'is_end': True}
        }
    }
}
# 邻接矩阵：i行j列表示从顶点i到顶点j的有向路径的权重。适用于小规模的图。
```

**dfs与bfs**

```python
from collections import deque, defaultdict
def bfs(graph, start_node):
    queue = deque([start_node])
    visited = set()
    visited.add(start_node)
    while queue:
        current_node = queue.popleft()
        for neighbor in graph[current_node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
                
#Graph类实现dfs
class Graph:
    def __init__(self):
        self.graph = defaultdict(list)

    def addEdge(self, u, v):
        self.graph[u].append(v)

    def DFS(self, v, visited=None):
        if visited is None:
            visited = set()
        visited.add(v)
        print(v, end=' ')
        for neighbour in self.graph[v]:
            if neighbour not in visited:
                self.DFS(neighbour, visited)
```

**强连通单元**

有向图 G**强连通**是指 G中任意两点强连通, 即这两个点从其一到另一都有路径. **强连通分量** 是指极大的强连通子图. 

无向图 G**连通**是指 G 中任意两点连通, 即这两点之间有路径. **连通分量**是指极大的连通子图.

**Kosaraju算法**是一种用于在**有向图中寻找强连通分量**（Strongly Connected Components，SCC）的算法。它基于深度优先搜索（DFS）和图的转置操作。Kosaraju算法的核心思想就是两次深度优先搜索（DFS）。

```python
def dfs1(graph, node, visited, stack):
    visited[node] = True
    for neighbor in graph[node]:
        if not visited[neighbor]:
            dfs1(graph, neighbor, visited, stack)
    stack.append(node)

def dfs2(graph, node, visited, component):
    visited[node] = True
    component.append(node)
    for neighbor in graph[node]:
        if not visited[neighbor]:
            dfs2(graph, neighbor, visited, component)

def kosaraju(graph):
    # Step 1: 标准的深度优先搜索，但是在此过程中，我们记录下顶点完成搜索的顺序。目的是找出每个顶点的完成时间（即结束时间）
    stack = []
    visited = [False] * len(graph)
    for node in range(len(graph)):
        if not visited[node]:
            dfs1(graph, node, visited, stack)
    
    # Step 2: 对原图取反，即将所有的边方向反转，得到反向图
    transposed_graph = [[] for _ in range(len(graph))]
    for node in range(len(graph)):
        for neighbor in graph[node]:
            transposed_graph[neighbor].append(node)
    
    # Step 3: 按照第一步中记录的顶点完成时间的逆序，对反向图进行DFS。这样，我们将找出反向图中的强连通分量。
    visited = [False] * len(graph)
    sccs = []
    while stack:
        node = stack.pop()
        if not visited[node]:
            scc = []
            dfs2(transposed_graph, node, visited, scc)
            sccs.append(scc)
    return sccs

# Example
graph = [[1], [2, 4], [3, 5], [0, 6], [5], [4], [7], [5, 6]]
sccs = kosaraju(graph)
print("Strongly Connected Components:")
for scc in sccs:
    print(scc)

"""
Strongly Connected Components:
[0, 3, 2, 1]
[6, 7]
[5, 4]
"""
```

**拓扑排序**（Topological Sorting）是对**有向无环图**（DAG）进行排序的一种算法。它将图中的顶点按照一种线性顺序进行排列，使得对于**任意的有向边 (u, v)，顶点 u 在排序中出现在顶点 v 的前面**。

Kahn算法是基于广度优先搜索（BFS）的一种拓扑排序算法。步骤：

1. 初始化一个队列，用于存储当前入度为0的顶点。
2. 遍历图中的所有顶点，计算每个顶点的入度，并将入度为0的顶点加入到队列中。
3. 不断地从队列中弹出顶点，并将其加入到拓扑排序的结果中。同时，遍历该顶点的邻居，并将其入度减1。如果某个邻居的入度减为0，则将其加入到队列中。
4. 重复步骤3，直到队列为空。

```python
from collections import deque
def topo_sort(graph):
    in_degree={u:0 for u in graph}
    for u in graph:
        for v in graph[u]:
            in_degree[v]+=1
    q=deque([u for u in in_degree if in_degree[u]==0])
    topo_order=[]
    while q:
        u=q.popleft()
        topo_order.append(u)
        for v in graph[u]:
            in_degree[v]-=1
            if in_degree[v]==0:
                q.append(v)
    if len(topo_order)!=len(graph):
        return []  
    return topo_order
# 示例调用代码
graph = {
    'A': ['B', 'C'],
    'B': ['C', 'D'],
    'C': ['E'],
    'D': ['F'],
    'E': ['F'],
    'F': []
}
sorted_vertices = topo_sort(graph)
if sorted_vertices:
    print("Topological sort order:", sorted_vertices)
else:
    print("The graph contains a cycle.")
# Output:
# Topological sort order: ['A', 'B', 'C', 'D', 'E', 'F']
```

**最短路径：有权图Dijkstra, 无权图BFS**

- BFS使用队列来保存待访问的顶点，并按照顺序进行遍历。它不考虑权重，只关注路径的长度。
- Dijkstra算法使用优先队列（通常是最小堆）来保存待访问的顶点，并按照顶点到起始顶点的距离进行排序。它根据路径长度来决定下一个要访问的顶点，从而保证每次都是选择最短路径的顶点进行访问。

```python
#dijkstra
# 1.使用vis集合 graph：二维列表，grapg[i][j]表示i到j的权值
from heapq import *
def dijkstra(start,end):
    heap=[(0,start,[start])]
    vis=set()
    while heap:
        (cost,u,path)=heappop(heap)
        if u in vis:
            continue
        vis.add(u)
        if u == end:
            return (cost,path)
        for v in graph[u]:
            if v not in vis:
                heappush(heap,(cost+graph[u][v],v,path+[v]))

# 2.使用dist数组 graph：字典，键=当前节点A，值=（节点B，A到B的权值）
import heapq
def dijkstra(graph, start):
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    priority_queue = [(0, start)]
    while priority_queue:
        current_distance, current_node = heapq.heappop(priority_queue)
        if current_distance > distances[current_node]:
            continue
        for neighbor, weight in graph[current_node].items():
            distance = current_distance + weight
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(priority_queue, (distance, neighbor))
    return distances
```

### 最小生成树

将图转化为树，要求：1.不能有环路存在（节点数为N，合法的边数为N-1）；2.权值和最小

#### 1.kruskal算法

1. 将图中的所有边按照权重从小到大进行排序。（队列）

2. 初始化一个空的边集，用于存储最小生成树的边。

3. 重复以下步骤，直到边集中的边数等于顶点数减一或者所有边都已经考虑完毕：（用并查集判断新加入的边是否合法）

   - 选择排序后的边集中权重最小的边。
   - 如果选择的边不会导致形成环路（即加入该边后，两个顶点不在同一个连通分量中），则将该边加入最小生成树的边集中。

4. 返回最小生成树的边集作为结果。

Kruskal算法的核心思想是通过**不断选择权重最小的边，并判断是否会形成环路来构建最小生成树**。算法开始时，每个顶点都是一个独立的连通分量，随着边的不断加入，不同的连通分量逐渐合并为一个连通分量，直到最终形成最小生成树。

实现Kruskal算法时，一种常用的数据结构是并查集（Disjoint Set）。并查集可以高效地判断两个顶点是否在同一个连通分量中，并将不同的连通分量合并。

```python
##class DisjointSet:
''''''
##graph：二维列表，grapg[i][j]表示i到j的权值
def kruskal(graph):
    num_vertices = len(graph)
    edges = []
    # 构建边集
    for i in range(num_vertices):
        for j in range(i + 1, num_vertices):
            if graph[i][j] != 0:
                edges.append((i, j, graph[i][j]))
    # 按照权重排序
    edges.sort(key=lambda x: x[2])
    # 初始化并查集
    disjoint_set = DisjointSet(num_vertices)
    # 构建最小生成树的边集
    minimum_spanning_tree = []
    for edge in edges:
        u, v, weight = edge
        if disjoint_set.find(u) != disjoint_set.find(v):
            disjoint_set.union(u, v)
            minimum_spanning_tree.append((u, v, weight))
    return minimum_spanning_tree
```

#### 2.Prim算法

可以从任何一个节点开始，找距离已选节点最近的那个点。然后将连接该边和该点的权值加入进去，作为最小生成树的一条边。

重复这样操作，直到所有节点都进入。prim更适用于稠密图

**最小生成树：有权图Prim, 无权图BFS**

- Prim算法是一种用于解决最小生成树（MST）问题的贪心算法，它会逐步构建一个包含所有顶点的树，并且使得树的边权重之和最小。
- BFS是一种用于无权图的遍历算法，它按照层次遍历的方式访问图的所有节点，并找到从起始顶点到其他所有顶点的最短路径。
- Prim算法通过选择具有最小权重的边来扩展生成树，并且只考虑与当前生成树相邻的顶点。
- BFS通过队列来保存待访问的顶点，并按照顺序进行遍历，不考虑边的权重。

```python
#graph：字典，键=当前节点A，值=（A到B的权值，节点B）
from heapq import heappop, heappush, heapify

def prim(graph, start_node):
    mst = set()
    visited = set([start_node])
    edges = [(cost, start_node, to) for to, cost in graph[start_node].items()]
    heapify(edges)

    while edges:
        cost, frm, to = heappop(edges)
        if to not in visited:
            visited.add(to)
            mst.add((frm, to, cost))
            for to_next, cost2 in graph[to].items():
                if to_next not in visited:
                    heappush(edges, (cost2, to, to_next))

    return mst
```

