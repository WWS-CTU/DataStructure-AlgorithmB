# Assignment #4: 排序、栈、队列和树

Updated 0005 GMT+8 March 11, 2024

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

### 05902: 双端队列

http://cs101.openjudge.cn/practice/05902/



思路：

在Queue基础之上，只需重新定义dequeue_new操作。注意ans = reversed(queue.items)这一步，即使队列空了，ans也不是[]，所以需要用len或者新定义size()函数判定。

代码

```python
# 
class Queue:
    def __init__(self):
        self.items = []
    def enqueue(self, element):
        self.items.insert(0, element)
    def dequeue_normal(self):
        if len(self.items) > 0:
            return self.items.pop()
        else:
            return None
    def dequeue_new(self):
        if len(self.items) > 0:
            a = self.items[0]
            self.items = self.items[1::]
            return a
        else:
            return None
    def size(self):
        return len(self.items)
t = int(input())
for _ in range(t):
    n = int(input())
    queue = Queue()
    for k in range(n):
        type, c = map(str, input().split())
        if type == '1':
            queue.enqueue(c)
        if type == '2':
            if c == '0':
                queue.dequeue_normal()
            if c == '1':
                queue.dequeue_new()
    ans = reversed(queue.items)
    if queue.size() == 0:
        print('NULL')
    else:
        print(' '.join(ans))
```



代码运行截图 ==（至少包含有"Accepted"）==

12min完成

![image-20240311165143044](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240311165143044.png)

### 02694: 波兰表达式

http://cs101.openjudge.cn/practice/02694/



思路：

从最后开始遍历，遇到运算符就计算该运算符和后两个数，直到列表长度为1



代码

```python
# 
s = input().split()
k = 0
while True:
    if len(s) == 1:
        break
    if s[-k-1] in '+-*/':
        if s[-k-1] == '+':
            left = s[:-k-1:]
            right = s[-k+2+len(s)::]
            s = left + [str(float(s[-k]) + float(s[-k+1]))] + right
            k = 0
        if s[-k-1] == '-':
            left = s[:-k-1:]
            right = s[-k+2+len(s)::]
            s = left + [str(float(s[-k]) - float(s[-k+1]))] + right
            k = 0
        if s[-k-1] == '*':
            left = s[:-k-1:]
            right = s[-k+2+len(s)::]
            s = left + [str(float(s[-k]) * float(s[-k+1]))] + right
            k = 0
        if s[-k-1] == '/':
            left = s[:-k-1:]
            right = s[-k+2+len(s)::]
            s = left + [str(float(s[-k]) / float(s[-k+1]))] + right
            k = 0
    else:
        k += 1
print("%.6f" % float(s[0]))
```



代码运行截图 ==（至少包含有"Accepted"）==

20min完成

![image-20240311191957645](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240311191957645.png)



### 24591: 中序表达式转后序表达式

http://cs101.openjudge.cn/practice/24591/



思路：

依次遍历整个表达式

左括号：压入栈

数：需要找到这个数对应的位置（即下一个运算符或括号的位置）之后，直接存储到ans

右括号：不断弹出栈顶元素并储存到ans，直到遇到左括号，弹出左括号。

+-*/：如果当前元素优先级高于栈顶元素，直接压入栈；如果当前元素优先级不高于栈顶元素，弹出栈顶元素，继续比较。（优先级：乘除大于加减大于括号）

遍历完成之后，依次弹出栈内元素。

代码

```python
# 
class Stack:
    def __init__(self):
        self.items = []
    def push(self, element):
        self.items.append(element)
    def pop(self):
        if len(self.items) > 0:
            return self.items.pop()
        else:
            return None
    def size(self):
        return len(self.items)
    def is_empty(self):
        return len(self.items) == 0
    def get_top(self):
        return self.items[-1] if len(self.items) > 0 else None
n = int(input())
for _ in range(n):
    s = input()
    ans = []
    stack = Stack()
    i = 0
    l = len(s)
    while i <= l - 1:
        if s[i] in '+-*/()':
            if s[i] == '(':
                stack.push(s[i])
            if s[i] == ')':
                while True:
                    p = stack.get_top()
                    if p != '(':
                        ans.append(p)
                        stack.pop()
                    else:
                        stack.pop()
                        break
            if s[i] == '+' or s[i] == '-':
                while stack.get_top() != '(' and (not stack.is_empty()):
                    ans.append(stack.pop())
                stack.push(s[i])
            if s[i] == '*' or s[i] == '/':
                while stack.get_top() == '*' or stack.get_top() == '/':
                    ans.append(stack.pop())
                stack.push(s[i])
            i += 1
        else:
            k1, k2, k3, k4, k5, k6 = l, l, l, l, l, l
            if '+' in s[i::]:
                k1 = s[i::].index('+')
            if '-' in s[i::]:
                k2 = s[i::].index('-')
            if '*' in s[i::]:
                k3 = s[i::].index('*')
            if '/' in s[i::]:
                k4 = s[i::].index('/')
            if '(' in s[i::]:
                k5 = s[i::].index('(')
            if ')' in s[i::]:
                k6 = s[i::].index(')')
            k = min(k1, k2, k3, k4, k5, k6)
            ans.append(s[i:i + k:])
            i = i + k
    while not stack.is_empty():
        ans.append(stack.pop())
    print(' '.join(ans))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

1h完成

![image-20240311205526172](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240311205526172.png)



### 22068: 合法出栈序列

http://cs101.openjudge.cn/practice/22068/



思路：

k=0，从待检测序列第一个元素起，如果它与栈顶元素不同，那么把原序列的第k+1个元素压入栈，k+1，再比对。如果相同，那么就pop。如果压完全部元素都没有匹配成功，那就是非法序列。最后只要完成全部匹配（栈空），就是合法的。

代码

```python
# 
class Stack:
    def __init__(self):
        self.items = []
    def push(self, element):
        self.items.append(element)
    def push_string(self, string):
        for elem in string:
            self.push(elem)
    def size(self):
        return len(self.items)
    def is_empty(self):
        return len(self.items) == 0
    def pop(self):
        if len(self.items) == 0:
            return None
        return self.items.pop()
    def get_top(self):
        return self.items[-1] if len(self.items) > 0 else None

def judge(original, s):
    if len(original) != len(s):
        return False
    stack = Stack()
    k = 0
    for i in range(len(s)):
        while stack.get_top() != s[i]:
            if k >= len(original):
                return False
            stack.push(original[k])
            k += 1
        stack.pop()
    return True if stack.is_empty() else False

element = input().strip()
while True:
    try:
        s = input().strip()
    except EOFError:
        break
    if judge(element, s):
        print('YES')
    else:
        print('NO')
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

20min完成

![image-20240314104513739](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240314104513739.png)

### 06646: 二叉树的深度

http://cs101.openjudge.cn/practice/06646/



思路：

自己在看过tree_questions讲义后，不看答案敲了一遍代码

代码

```python
# 
class TreeNode:
    def __init__(self):
        self.left_child = None
        self.right_child = None

def tree_depth(node):
    if node == None:
        return 0
    left_depth = tree_depth(node.left_child)
    right_depth = tree_depth(node.right_child)
    return max(left_depth, right_depth) + 1

n = int(input())
nodes = [TreeNode() for _ in range(n)]
for i in range(n):
    a, b = map(int, input().split())
    if a != -1:
        nodes[i].left_child = nodes[a - 1]
    if b != -1:
        nodes[i].right_child = nodes[b - 1]

print(tree_depth(nodes[0]))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

5min完成

![image-20240314100458932](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240314100458932.png)



### 02299: Ultra-QuickSort

http://cs101.openjudge.cn/practice/02299/



思路：

归并的同时+逆序对计数（左数组逆序对数+右数组逆序对数+跨越两数组的逆序对数）

跨越两个数组的逆序对，可以通过排序后的序关系较方便的算出来

代码

```python
# 
def merge_sort(lst):
    if len(lst) < 2:
        return lst, 0
    mid = len(lst) // 2
    left = lst[:mid:]
    right = lst[mid::]
    left = merge_sort(left)
    right = merge_sort(right)
    return merge(left, right)

def merge(left, right):
    i = j = inv = 0
    tmp = []
    while i < len(left) and j < len(right):
        if left[i] > right[j]:
            tmp.append(right[j])
            j += 1
            inv += len(left) - i
        elif left[i] < right[j]:
            tmp.append(left[i])
            i += 1
    tmp += left[i::]
    tmp += right[j::]
    return tmp, inv

def count_inverse(lst):
    if len(lst) < 2:
        return (lst, 0)
    mid = len(lst) // 2
    left, inv1 = count_inverse(lst[:mid:])
    right, inv2 = count_inverse(lst[mid::])
    merged, inv = merge(left, right)
    inv += inv1 + inv2
    return merged, inv

while True:
    n = int(input())
    if n == 0:
        break
    lst = []
    for _ in range(n):
        lst.append(int(input()))
    print(count_inverse(lst)[-1])
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

1h完成

![image-20240314115141167](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240314115141167.png)



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==



这次作业题目已经比较难了，现在正在抓紧往前自学树的内容。作业耗时较多，还需要定期回顾曾经没AC的题。

