# Assignment #7: April 月考

Updated 1557 GMT+8 Apr 3, 2024

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

### 27706: 逐词倒放

http://cs101.openjudge.cn/practice/27706/



思路：

直接reverse

代码

```python
# 
word_list = input().split()
word_list.reverse()
print(' '.join(word_list))
```



代码运行截图 ==（至少包含有"Accepted"）==

![image-20240403170804707](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240403170804707.png)



### 27951: 机器翻译

http://cs101.openjudge.cn/practice/27951/



思路：

直接模拟即可。但考试的时候以为queue的长度达不到m，所以if判断那里写的m，然后WA了。好在最后成功纠错。

代码

```python
# 
m, n = map(int, input().split())
lst = list(map(int, input().split()))
ans = 0
queue = []
for word in lst:
    if word not in queue:
        ans += 1
        queue.insert(0, word)
        if len(queue) == m + 1:
            queue.pop()
print(ans)
```



代码运行截图 ==（至少包含有"Accepted"）==

![image-20240403194842046](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240403194842046.png)



### 27932: Less or Equal

http://cs101.openjudge.cn/practice/27932/



思路：

直接做。坑点在于k=0和k=n

代码

```python
# 
n, k = map(int, input().split())
lst = list(map(int, input().split()))
ans = -1
lst.sort()
if k == 0:
    if 1 < lst[0]:
        ans = 1
elif k == n:
    ans = lst[-1]
else:
    if lst[k] != lst[k - 1]:
        ans = lst[k-1]
print(ans)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240403194952762](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240403194952762.png)



### 27948: FBI树

http://cs101.openjudge.cn/practice/27948/



思路：

递归地建树即可。递归终点是字符串长度为1。后序遍历也已经写过很多次了

代码

```python
# 
class BinaryTreeNode:
    def __init__(self, value):
        self.value = value
        self.left = None
        self.right = None

def postorder(root):
    left = postorder(root.left) if root.left else []
    right = postorder(root.right) if root.right else []
    return left + right + [root.value]

def BuildTree(s):
    root_val = None
    if '0' in s and '1' in s:
        root_val = 'F'
    elif '0' in s:
        root_val = 'B'
    elif '1' in s:
        root_val = 'I'
    root = BinaryTreeNode(root_val)
    l = len(s)
    if l == 1:
        return root
    root.left = BuildTree(s[:l//2:])
    root.right = BuildTree(s[l//2::])
    return root

n = int(input())
s = input()
root = BuildTree(s)
ans = postorder(root)
print(''.join(ans))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240403195037558](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240403195037558.png)



### 27925: 小组队列

http://cs101.openjudge.cn/practice/27925/



思路：

考试时感觉最难的一道题。在考试结束前5分钟才做出来。

我的思路是用两个队列，分别记录每个小组在队列中的人的编号，以及在队列中的小组编号（显然能够证明，队列里面，同一小组的成员必定相邻。于是整个队列可以被划分为若干“子队列”，每个子队列都是同一组，这样这些在队列中的小组又可以用一个队列表示。）

处理技巧上，仿照了c++的预设数组，卡着数据范围建了列表。

代码

```python
# 
t = int(input())
lst = [0] * 1000005
for _ in range(t):
    inp = list(map(int, input().split()))
    for person in inp:
        lst[person] = _
queue_team, queue_person = [], [[] for i in range(1005)]
while True:
    command = input()
    if command == 'STOP':
        break
    if command == 'DEQUEUE':
        idx = queue_team[0]
        print(queue_person[idx][0])
        del(queue_person[idx][0])
        if not queue_person[idx]:
            del(queue_team[0])
    else:
        enqueue = int(command[8::])
        idx = lst[enqueue]
        if queue_person[idx] == []:
            queue_team.append(idx)
        queue_person[idx].append(enqueue)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240403195434367](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240403195434367.png)



### 27928: 遍历树

http://cs101.openjudge.cn/practice/27928/



思路：

首先要找树根。注意到输入的每一行第一个数互不相同，恰好他们是所有的节点值。而根节点只会在后续出现1次，叶子节点恰好会出现两次（这个很容易证明）。据此可以找到根节点。（处理技巧上，运用了set的减法）

然后建树的过程又是递归，从树根开始。递归终点是该节点无叶子节点，直接返回TreeNode(root_val)即可。

最后还要写题目要求的遍历顺序。依旧是递归。考虑当前节点和全部子节点的大小，从小的开始，如果是子树就递归调用，如果是当前节点就直接加入遍历序列。注意递归边界：子树为空。

代码

```python
# 
class TreeNode:
    def __init__(self, value):
        self.value = value
        self.children = []

def BuildTree(lst, root_val):
    root = TreeNode(root_val)
    if lst == []:
        return root
    for line in lst:
        if line[0] == root_val:
            if len(line) == 1:
                return root
            for num in line[1::]:
                root.children.append(BuildTree(lst, num))
    return root

def order(root):
    ans = []
    if not root.children:
        return [root.value]
    lst = [child.value for child in root.children] + [root.value]
    lst.sort()
    for num in lst:
        if num != root.value:
            for child in root.children:
                if child.value == num:
                    ans.extend(order(child))
        else:
            ans.append(root.value)
    return ans


n = int(input())
lst, val, lst1 = [], [], []
for _ in range(n):
    inp = list(map(int, input().split()))
    lst.append(inp)
    val.append(inp[0])
    if len(inp) > 1:
        lst1.extend(inp[1::])
root_val = list(set(val) - set(lst1))[0]
root = BuildTree(lst, root_val)
ans = order(root)
for element in ans:
    print(element)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240403195642769](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240403195642769.png)



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

考试题目难度还是比较合理。有些题几分钟，几行代码就能搞定；有些题目则需要深究，从数据结构的选取，到具体代码的实现，中间还有很多需要处理的细节，这些细节需要计概的深厚功底（比如set的减法，字典操作etc.）+对于题目的深刻理解才能做到位。



