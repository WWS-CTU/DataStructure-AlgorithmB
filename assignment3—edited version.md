# Assignment #3: March月考

Updated 1537 GMT+8 March 6, 2024

2024 spring, Complied by ==王伟圣 数学科学学院==



**说明：**

1）The complete process to learn DSA from scratch can be broken into 4 parts:
- Learn about Time and Space complexities
- Learn the basics of individual Data Structures
- Learn the basics of Algorithms
- Practice Problems on DSA

2）请把每个题目解题思路（可选），源码Python, 或者C++（已经在Codeforces/Openjudge上AC），截图（包含Accepted），填写到下面作业模版中（推荐使用 typora https://typoraio.cn ，或者用word）。AC 或者没有AC，都请标上每个题目大致花费时间。

3）提交时候先提交pdf文件，再把md或者doc文件上传到右侧“作业评论”。Canvas需要有同学清晰头像、提交文件有pdf、"作业评论"区有上传的md或者doc附件。

4）如果不能在截止前提交作业，请写明原因。



**编程环境**

==（请改为同学的操作系统、编程环境等）==

操作系统：macOS Ventura 13.4.1 (c)

Python编程环境：Spyder IDE 5.2.2, PyCharm 2023.1.4 (Professional Edition)

C/C++编程环境：Mac terminal vi (version 9.0.1424), g++/gcc (Apple clang version 14.0.3, clang-1403.0.22.14.1)



## 1. 题目

**02945: 拦截导弹**

http://cs101.openjudge.cn/practice/02945/



思路：

dp，递归终点是只剩1个炮弹需要拦截。dp时注意记录当前最大拦截高度

##### 代码

```python
# 
def dp(lst, m):
    if len(lst) == 1:
        return m >= lst[0]
    if lst[0] > m:
        return dp(lst[1::], m)
    else:
        return max(dp(lst[1::], lst[0]) + 1, dp(lst[1::], m))
k = int(input())
lst = list(map(int,input().split()))
print(dp(lst, max(lst)+1))

```



代码运行截图 ==（至少包含有"Accepted"）==

![image-20240306225738646](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240306225738646.png)



**04147:汉诺塔问题(Tower of Hanoi)**

http://cs101.openjudge.cn/practice/04147



思路：

递归。整个操作分为三大步：

step1：把上面n-1个圆盘移到B（n-1子问题）

step2：把最底下的圆盘移到C（n=1的子问题）

step3：把n-1个圆盘移到C（n-1的子问题）

注意记录圆盘编号

##### 代码

```python
# 
def move(n, sfrom, sto):
    print(str(n) + ':' + sfrom + '->' + sto)
def henoi(n, sfrom, sto, sby):
    if n == 1:
        move(1, sfrom, sto)
        return
    henoi(n-1, sfrom, sby, sto)
    move(n, sfrom, sto)
    henoi(n-1, sby, sto, sfrom)
s = input().split()
n = int(s[0])
a, b, c = s[1], s[2], s[3]
henoi(n, a, c, b)
```



代码运行截图 ==（至少包含有"Accepted"）==

![image-20240306225902778](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240306225902778.png)



**03253: 约瑟夫问题No.2**

http://cs101.openjudge.cn/practice/03253



思路：

用了队列实现。每m次为1个循环，不出圈就是出队之后立刻入队，否则只出队不入队。最后把顺序依次移动p-1个位次即可

##### 代码

```python
# 
class Queue:
    def __init__(self):
        self.items = []
    def enqueue(self, element):
        self.items.insert(0, element)
    def dequeue(self):
        if len(self.items) > 0:
            return self.items.pop()
        else:
            return None
    def size(self):
        return len(self.items)
while True:
    n, p, m = map(int, input().split())
    if (n, p, m) == (0, 0, 0):
        break
    queue = Queue()
    order = []
    for i in range(n):
        queue.enqueue(i+1)
    while queue.size() > 1:
        for k in range(m-1):
            queue.enqueue(queue.dequeue())
        x = (queue.dequeue() + p - 1) % n
        if x:
            order.append(str(x))
        else:
            order.append(str(n))
    x = (queue.dequeue() + p - 1) %n
    if x:
        order.append(str(x))
    else:
        order.append(str(n))
    print(','.join(order))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240306230244528](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240306230244528.png)



**21554:排队做实验 (greedy)v0.2**

http://cs101.openjudge.cn/practice/21554



思路：

小学奥数题。时间短的排在前面，等待总时间一定最短。排序时注意用index函数检索第一个下标，修改值为max+1可以保证顺序

##### 代码

```python
# 
n = int(input())
timelst = list(map(int, input().split()))
new_timelst = sorted(timelst)
order = []
total, num = 0, 1
for t in new_timelst:
    i = timelst.index(t)
    timelst[i] = new_timelst[-1] + 1
    order.append(str(i+1))
    total += t * (n-num)
    num += 1
print(' '.join(order))
print("%.2f" % (total / n))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240306230453839](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240306230453839.png)



**19963:买学区房**

http://cs101.openjudge.cn/practice/19963



思路：

还好吧……照着题目说的做就行（但考场没想到statistics库qwq）

##### 代码

```python
# 
n = int(input())
pairs = [i[1:-1] for i in input().split()]
distances = [ sum(map(int,i.split(','))) for i in pairs]
valuelst = list(map(int, input().split()))
xjb = [distances[i] / valuelst[i] for i in range(n)]
ans = 0
xjb1 = sorted(xjb)
valuelst1 = sorted(valuelst)
if n % 2 == 0:
    xjb_mid = ( xjb1[n // 2] + xjb1[n // 2 - 1] ) / 2
    value_mid = ( valuelst1[n // 2] + valuelst1[n // 2 - 1] ) / 2
else:
    xjb_mid = xjb1[(n-1)//2]
    value_mid = valuelst1[(n-1)//2]
for i in range(n):
    if xjb[i] > xjb_mid and valuelst[i] < value_mid:
        ans += 1
print(ans)
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240306230523232](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240306230523232.png)



**27300: 模型整理**

http://cs101.openjudge.cn/practice/27300



思路：

整体思路是建立模型名称和存储容量列表的键值对。细节比较多。

1、对M和B的处理。本人采用暴力换算，全部转换成M的对应数值（要讨论浮点数字符串和非浮点数字符串）

2、排序之后如何转化回去。python的浮点数运算不精确。为了防止出什么岔子，我依旧选择了分类讨论（能被1000整除和不能被1000

整除）

##### 代码

```python
# 
n = int(input())
dic = {}
for i in range(n):
    s = input().split('-')
    name = s[0]
    if s[1][-1] == 'M':
        temp = s[1][:-1:]
        if '.' in temp:
            a, b = map(int, temp.split('.'))
            num = a + b / pow(10, len(str(b)))
        else:
            num = int(temp)
    else:
        temp = s[1][:-1:]
        if '.' in temp:
            a, b = map(int, temp.split('.'))
            num = a * 1000 + b * pow(10, 3 - len(str(b)))
        else:
            num = int(temp) * 1000
    if name in dic.keys():
        dic[name].append(num)
    else:
        dic[name] = [num]
namelst = sorted(dic.keys())
for name in namelst:
    dic[name].sort()
    for i in range(len(dic[name])):
        if dic[name][i] >= 1000:
            real = dic[name][i] // 1000 if dic[name][i] % 1000 == 0 else dic[name][i] / 1000
            dic[name][i] = str(real) + 'B'
        else:
            dic[name][i] = str(dic[name][i]) + 'M'
    print(name + ': ' + ', '.join(dic[name]))
```



代码运行截图 ==（AC代码截图，至少包含有"Accepted"）==

![image-20240306230636517](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240306230636517.png)



## 2. 学习总结和收获

==如果作业题目简单，有否额外练习题目，比如：OJ“2024spring每日选做”、CF、LeetCode、洛谷等网站题目。==

这次月考题目感觉和上学期计概期末上机难度差不多。后面两道Tough的题，其实不涉及什么特殊算法，只需要把题目读懂，想到细节问

题就能AC。

通过月考，重温了queue的常见操作（虽然不用类也能写），强迫自己写了dp并第一次AC（去年计概的dp题全部都WA），henoi塔的

题，计概班上讲过，所以最后回忆起来了，但一开始没有成功唤起回忆。



