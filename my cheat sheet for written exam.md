# cheat sheet

#### KMP算法

KMP 算法是用来处理**字符串匹配问题**的，也就是给定两个字符串 𝐴,𝐵 ，求 𝐵 是否是 𝐴 的子串， 𝐴 的长度为 𝑛 ， 𝐵 的长度为 𝑚 ，其中我们称 𝐴 为主串， 𝐵 为匹配串，假设 𝐴="myfriend",𝐵="fri" ，此时 𝐵 就是 𝐴 的子串。KMP算法的时间复杂度为O(n+m)

目标字符串ptr = ababaca，

**next数组**的含义就是一个固定字符串的**最长前缀和最长后缀相同的长度**。

比如：abcjkdabc，那么这个数组的最长前缀和最长后缀相同必然是abc。cbcbc，最长前缀和最长后缀相同是cbc。abcbc，最长前缀和最长后缀相同（整个字符串），是不存在的。

对于目标字符串ptr，ababaca，长度是7，所以next[0]，next[1]，next[2]，next[3]，next[4]，next[5]，next[6]分别计算的是a，ab，aba，abab，ababa，ababac，ababaca的相同的最长前缀和最长后缀的长度。由于a，ab，aba，abab，ababa，ababac，ababaca的相同的最长前缀和最长后缀是“”，“”，“a”，“ab”，“aba”，“”，“a”，所以next数组的值是[0,0,1,2,3,0,1]

下图中的1，2，3，4是一样的（都是最长前/后缀）。1-2之间的和3-4之间的字符也是一样的，现在我们发现字符A和字符B不一样；于是把下面的字符串往前移动，使3和2对齐，直接比较C和A是否一样。

![image-20240617212129840](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240617212129840.png)

```python
""""
compute_lps 函数用于计算[模式字符串]的next数组，
其中的每个元素表示[模式字符串]中当前位置之前的子串的最长相同前缀后缀的长度。
该函数使用了两个指针length和i，从模式字符串的第二个字符开始遍历。
"""
def compute_lps(pattern):
    """
    计算pattern字符串的最长前缀后缀（Longest Proper Prefix which is also Suffix）表
    :param pattern: 模式字符串
    :return: next数组
    """

    m = len(pattern)
    next_lst = [0] * m
    length = 0
    for i in range(1, m):
        while length > 0 and pattern[i] != pattern[length]:
            length = next_lst[length - 1]    # 跳过前面已经比较过的部分
        if pattern[i] == pattern[length]:
            length += 1
        next_lst[i] = length
    return next_lst

def kmp_search(text, pattern): #用pattern来匹配text
    n = len(text)
    m = len(pattern)
    if m == 0:
        return 0
    lps = compute_lps(pattern)
    matches = []
    j = 0  # j是pattern的索引
    for i in range(n):  # i是text的索引
        while j > 0 and text[i] != pattern[j]:
            j = lps[j - 1]
        if text[i] == pattern[j]:
            j += 1
        if j == m:
            matches.append(i - j + 1)
            j = lps[j - 1]
    return matches

text = "ABABABABCABABABABCABABABABC"
pattern = "ABABCABAB"
index = kmp_search(text, pattern)
print("pos matched：", index)
# pos matched： [4, 13]
```

#### 前序表达式 (波兰表达式) 求值

##### 递归

递归定义波兰表达式, 再用递归求解: **波兰表达式是一个数, 或者“操作符 波兰表达式 波兰表达式”**, 这是一个 **top-down** 的过程. 

##### 栈

**从后往前扫表达式**, 遇到数字压入栈, 遇到操作符从栈中弹出两个操作数 (注意顺序) 做计算, 运算完再压入栈, 扫完一遍表达式刚好运算完, 栈内唯一元素即运算结果；这是一个 **bottom-up** 的过程. 

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

**从前往后扫表达式**, 遇到数字压入栈, 遇到操作符从从栈中弹出两个操作数 (注意顺序) 做计算, 运算完再压入栈, 扫完一遍表达式刚好运算完, 栈内唯一元素即运算结果；这是一个 **bottom-up** 的过程. 

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

```python
def infix_to_postfix(expression):
    precedence = {'+':1, '-':1, '*':2, '/':2}
    stack = []
    postfix = []
    number = ''

    for char in expression:
        if char.isnumeric() or char == '.':
            number += char
        else:
            if number:
                num = float(number)
                postfix.append(int(num) if num.is_integer() else num)
                number = ''
            if char in '+-*/':
                while stack and stack[-1] in '+-*/' and precedence[char] <= precedence[stack[-1]]:
                    postfix.append(stack.pop())
                stack.append(char)
            elif char == '(':
                stack.append(char)
            elif char == ')':
                while stack and stack[-1] != '(':
                    postfix.append(stack.pop())
                stack.pop()

    if number:
        num = float(number)
        postfix.append(int(num) if num.is_integer() else num)

    while stack:
        postfix.append(stack.pop())

    return ' '.join(str(x) for x in postfix)

n = int(input())
for _ in range(n):
    expression = input()
    print(infix_to_postfix(expression))
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

### 栈

```python
class Stack:
    def __init__(self):
        self.items = []
    
    def is_empty(self):
        return self.items == []
    
    def push(self, item):
        self.items.append(item)
    
    def pop(self):
        return self.items.pop()
    
    def peek(self):
        return self.items[len(self.items)-1]
    
    def size(self):
        return len(self.items)
```

### 队列

```python
class Queue:
    def __init__(self):
        self.items = []

    def is_empty(self):
        return self.items == []

    def enqueue(self, item):
        self.items.insert(0, item)

    def dequeue(self):
        return self.items.pop()

    def size(self):
        return len(self.items)
```

### 线性表

**线性表**是一种逻辑结构，描述了元素按线性顺序排列的规则。常见的线性表存储方式有**数组**和**链表**，它们在不同场景下具有各自的优势和劣势。

**数组**是一种连续存储结构，它将线性表的元素按照一定的顺序依次存储在内存中的连续地址空间上。数组需要预先分配一定的内存空间，每个元素占用相同大小的内存空间，并可以通过索引来进行快速访问和操作元素。**数组访问元素的时间复杂度为O(1)**，因为可以直接计算元素的内存地址。然而，**插入和删除元素的时间复杂度较高，平均为O(n)**，因为需要移动其他元素来保持连续存储的特性。

**链表**是一种存储结构，它是线性表的链式存储方式。链表通过节点的相互链接来实现元素的存储。每个节点包含元素本身以及指向下一个节点的指针。**链表的插入和删除操作非常高效，时间复杂度为O(1)**，因为只需要调整节点的指针。然而，**访问元素的时间复杂度较高，平均为O(n)**，因为必须从头节点开始遍历链表直到找到目标元素。

选择使用数组还是链表作为存储方式取决于具体问题的需求和限制。如果需要频繁进行随机访问操作，数组是更好的选择。如果需要频繁进行插入和删除操作，链表更适合。通过了解它们的特点和性能，可以根据实际情况做出选择。

**链表**是一种常见的数据结构，用于存储和组织数据。它由一系列节点组成，每个节点包含**一个数据元素和一个指向下一个节点（或前一个节点）的指针**。

在链表中，每个节点都包含两部分：

1. 数据元素（或数据项）：这是节点存储的实际数据。可以是任何数据类型，例如整数、字符串、对象等。
2. 指针（或引用）：该指针指向链表中的下一个节点（或前一个节点）。它们用于建立节点之间的连接关系，从而形成链表的结构。

根据指针的类型和连接方式，链表可以分为不同类型，包括：

#### 1、单链表

​	在链式结构中，除了要存储数据元素的信息外，还要存储它的后继元素的存储地址。

​	因此，为了表示**每个数据元素$$a_{i}$$与其直接后继元素$$a_{i+1}$$之间的逻辑关系，对数据$$a_{i}$$来说，除了存储其本身的信息之外，还需要存储一个指示其直接后继的信息（即直接后继的存储位置）。我们把存储数据元素信息的域称为数据域，把存储直接后继位置的域称为指针域。指针域中存储的信息称做指针或链。这两部分信息组成数据元素$$a_{i}$$的存储映像，称为结点（$$Node$$​）。**

​	我们把链表中第一个结点的存储位置叫做头指针。有时为了方便对对链表进行操作，会在单链表的第一个结点前附设一个节点，称为头结点，此时头指针指向的结点就是头结点。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210207165354972.png#pic_center)

​	空链表，头结点的直接后继为空。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210207165435359.png#pic_center)

#### 2、双链表

​	**双向链表$$(Double$$ $$Linked$$ $$List)$$​是在单链表的每个结点中，再设置一个指向其前驱结点的指针域。**所以在双向链表中的结点都有两个指针域，一个指向直接后继，另一个指向直接前驱。

##### 特点：

- **双向访问**：可以从任一节点双向遍历链表。
- **动态大小**：可以根据需要动态分配和释放内存。
- **插入和删除操作**：时间复杂度为 O(1)O(1)O(1)。

#### 3、循环链表

​	**将单链表中终端节点的指针端由空指针改为指向头结点，就使整个单链表形成一个环，这种头尾相接的单链表称为单循环链表，简称循环链表。**

​	然而这样会导致访问最后一个结点时需要$$O(n)$$的时间，所以我们可以写出**仅设尾指针的循环链表**。

链表相对于数组的一个重要特点是，链表的大小可以动态地增长或缩小，而不需要预先定义固定的大小。这使得链表在需要频繁插入和删除元素的场景中更加灵活。

然而，链表的访问和搜索操作相对较慢，因为需要遍历整个链表才能找到目标节点。与数组相比，链表的优势在于插入和删除操作的效率较高，尤其是在操作头部或尾部节点时。因此，链表在需要频繁插入和删除元素而不关心随机访问的情况下，是一种常用的数据结构。

**单向链表实现：**

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None
class LinkedList:
    def __init__(self):
        self.head = None
    def insert(self, value):
        new_node = Node(value)
        if self.head is None:
            self.head = new_node
        else:
            current = self.head
            while current.next:
                current = current.next
            current.next = new_node
    def delete(self, value):
        if self.head is None:
            return
        if self.head.value == value:
            self.head = self.head.next
        else:
            current = self.head
            while current.next:
                if current.next.value == value:
                    current.next = current.next.next
                    break
                current = current.next
    def display(self):
        current = self.head
        while current:
            print(current.value, end=" ")
            current = current.next
        print()
# 使用示例
linked_list = LinkedList()
linked_list.insert(1)
linked_list.insert(2)
linked_list.insert(3)
linked_list.display()  # 输出：1 2 3
linked_list.delete(2)
linked_list.display()  # 输出：1 3
```

**双向链表实现：**

```python
class Node:
    def __init__(self, value):
        self.value = value
        self.prev = None
        self.next = None
class DoublyLinkedList:
    def __init__(self):
        self.head = None
        self.tail = None
    def insert_before(self, node, new_node):
        if node is None:  # 如果链表为空，将新节点设置为头部和尾部
            self.head = new_node
            self.tail = new_node
        else:
            new_node.next = node
            new_node.prev = node.prev
            if node.prev is not None:
                node.prev.next = new_node
            else:  # 如果在头部插入新节点，更新头部指针
                self.head = new_node
            node.prev = new_node
    def display_forward(self): #正向遍历并显示链表中的所有节点
        current = self.head
        while current is not None:
            print(current.value, end=" ")
            current = current.next
        print()
    def display_backward(self): #反向遍历并显示链表中的所有节点
        current = self.tail
        while current is not None:
            print(current.value, end=" ")
            current = current.prev
        print()
# 使用示例
linked_list = DoublyLinkedList()
# 创建节点
node1 = Node(1)
node2 = Node(2)
node3 = Node(3)
# 将节点插入链表
linked_list.insert_before(None, node1)  # 在空链表中插入节点1
linked_list.insert_before(node1, node2)  # 在节点1前插入节点2
linked_list.insert_before(node1, node3)  # 在节点1前插入节点3
# 显示链表内容
linked_list.display_forward()  # 输出：3 2 1
linked_list.display_backward()  # 输出：1 2 3
```

**颠倒链表**

```python
class Node:
    def __init__(self, data, next=None):
        self.data, self.next = data, next
class LinkList:
    def __init__(self, lst):
        self.head = Node(lst[0])
        p = self.head
        for i in lst[1:]:
            node = Node(i)
            p.next = node
            p = p.next
    def reverse(self):  # 填空：实现函数
        prev = None
        curr = self.head
        while curr:
            next_node = curr.next
            curr.next = prev
            prev = curr
            curr = next_node
        self.head = prev
    def print(self):
        p = self.head
        while p:
            print(p.data, end=" ")
            p = p.next
        print()
a = list(map(int, input().split()))
a = LinkList(a)
a.reverse()
a.print()
```

**删除链表元素**

```python
class Node:
    def __init__(self, data, next=None):
        self.data, self.next = data, next
class LinkList:  # 循环链表
    def __init__(self):
        self.tail = None
        self.size = 0
    def isEmpty(self):
        return self.size == 0
    def pushFront(self, data):
        nd = Node(data)
        if self.tail == None:
            self.tail = nd
            nd.next = self.tail
        else:
            nd.next = self.tail.next
            self.tail.next = nd
        self.size += 1
    def pushBack(self, data):
        self.pushFront(data)
        self.tail = self.tail.next
    def popFront(self):
        if self.size == 0:
            return None
        else:
            nd = self.tail.next
            self.size -= 1
            if self.size == 0:
                self.tail = None
            else:
                self.tail.next = nd.next
        return nd.data
    def printList(self):
        if self.size > 0:
            ptr = self.tail.next
            while True:
                print(ptr.data, end=" ")
                if ptr == self.tail:
                    break
                ptr = ptr.next
            print("")
    def remove(self, data):  # 填空：实现函数
        if self.size == 0:
            return None
        else:
            ptr = self.tail
            while ptr.next.data != data:
                ptr = ptr.next
                if ptr == self.tail:
                    return False
            self.size -= 1
            if ptr.next == self.tail:
                self.tail = ptr
            ptr.next = ptr.next.next
            return True
t = int(input())
for i in range(t):
    lst = list(map(int, input().split()))
    lkList = LinkList()
    for x in lst:
        lkList.pushBack(x)
    lst = list(map(int, input().split()))
    for a in lst:
        result = lkList.remove(a)
        if result == True:
            lkList.printList()
        elif result == False:
            print("NOT FOUND")
        else:
            print("EMPTY")
    print("----------------")
"""
样例输入
2
1 2 3
3 2 2 9 5 1 1 4
1
9 88 1 23
样例输出
1 2 
1 
NOT FOUND
NOT FOUND
NOT FOUND
EMPTY
EMPTY
----------------
NOT FOUND
NOT FOUND
EMPTY
----------------
"""
```

**插入链表元素**

```python
class Node:
    def __init__(self, data, next=None):
        self.data, self.next = data, next
class LinkList:
    def __init__(self):
        self.head = None
    def initList(self, data):
        self.head = Node(data[0])
        p = self.head
        for i in data[1:]:
            node = Node(i)
            p.next = node
            p = p.next
    def insertCat(self):
        # 计算链表的长度
        length = 0
        p = self.head
        while p:
            length += 1
            p = p.next
        # 找到插入位置
        position = length // 2 if length % 2 == 0 else (length // 2) + 1
        p = self.head
        for _ in range(position - 1):
            p = p.next
        # 在插入位置处插入数字6
        node = Node(6)
        node.next = p.next
        p.next = node
    def printLk(self):
        p = self.head
        while p:
            print(p.data, end=" ")
            p = p.next
        print()
lst = list(map(int, input().split()))
lkList = LinkList()
lkList.initList(lst)
lkList.insertCat()
lkList.printLk()
"""
### 样例输入1
8 1 0 9 7 5
### 样例输入2
1 2 3
### 样例输出1
8 1 0 6 9 7 5
### 样例输出2
1 2 6 3
"""
```

# 1. 排序

- **稳定排序**：如果 a 原本在 b 的前面，且 a == b，排序之后 a 仍然在 b 的前面，则为稳定排序。（只涉及相邻元素一定是稳定排序）

  **非稳定排序**：如果 a 原本在 b 的前面，且 a == b，排序之后 a 可能不在 b 的前面，则为非稳定排序。

  **原地排序**：原地排序就是指在排序过程中不申请多余的存储空间，只利用原来存储待排数据的存储空间进行比较和交换的数据排序。

  **非原地排序：**需要利用额外的数组来辅助排序。

![img](https://pic.rmb.bdstatic.com/bjh/down/ce0c0a3b07941dee7cf28dc7d53c9c81.png?x-bce-process=image/watermark,bucket_baidu-rmb-video-cover-1,image_YmpoL25ld3MvNjUzZjZkMjRlMDJiNjdjZWU1NzEzODg0MDNhYTQ0YzQucG5n,type_RlpMYW5UaW5nSGVpU01HQg==,w_24,text_QOaMh-WNl-mSiOavleS4muiuvuiuoQ==,size_24,x_19,y_19,interval_2,color_FFFFFF,effect_softoutline,shc_000000,blr_2,align_1)

### 插入排序（升序版）

1、从数组第2个元素开始抽取元素。

2、把它与左边第一个元素比较，如果它左边第一个元素比它大，则继续与左边第二个元素比较下去，直到遇到不比它大的元素，然后插到这个元素的右边。

3、继续选取第3，4，….n个元素,重复步骤 2 ，选择适当的位置插入。

例子：1，3，2，4，6→抽出3，3左边的1<3，所以插在1右边→抽出2，2左边的3>2，2左边第二个元素1<2，所以插在1右边……

### 选择排序（升序版）

首先，找到数组中最小的那个元素，其次，将它和数组的第一个元素交换位置(如果第一个元素就是最小元素那么它就和自己交换)。其次，在剩下的元素中找到最小的元素，将它与数组的第二个元素交换位置。如此往复，直到将整个数组排序。这种方法我们称之为选择排序。

### 冒泡排序（升序版）

左边大于右边，则交换。一趟排下来最大的在右边，重复即可。最坏情况：原数组是降序的。

### 快速排序（升序版）

首先选一个主元，可以是第一个元素，也可以最后一个元素。假设数组arr的范围为[left, right]，即起始下标为left，末尾下标为right。

然后令变量i = left + 1，j = right。然后让 i 和 j 从数组的两边向中间扫描。i 向右遍历的过程中，如果遇到大于或等于主元的元素时，则停止移动，j向左遍历的过程中，如果遇到小于或等于主元的元素则停止移动。

当i和j都停止移动时，如果这时i < j，则交换 i, j 所指向的元素。然后继续重复上述过程，直到i >= j。

最后把主元与 j 指向的元素交换(当然，与i指向的交换也行)。这个时候，j 左边的元素一定小于或等于主元，而右边则大于或等于主元。

接下来对j左边和右边两半边进行递归即可。

### 堆排序

**构建好大顶堆或小顶堆结构，这样最上面的就是最大值或最小值。我们取出堆顶元素，然后重新构建堆结构，一直取，一直重新构建，那么最后达到排序的效果了。**以**小顶堆**为例（从小到大排序）：

第一步：把数组元素依次放入完全二叉树中

第二步：从最底层开始调整，形成小顶堆

**插入一个节点。**在插入的时候，我们把新节点插到完全二叉树的最后一个位置。之后进行调整：让新插入的节点与它的父节点进行比较，如果新节点小于父节点，则让新节点**上浮**，即和父节点交换位置。上浮之后继续和它的父节点进行比较，直到父节点的值小于或等于该节点，才停止上浮，即插入结束。

**删除节点。**把根节点删除之后，用二叉堆的最后一个元素顶替上来，成为新的根节点，然后采取**下沉**的策略恢复：让新的根节点和左右孩子节点相比较，如果左右节点有小于它的，则让那个**较小的孩子**代替根节点的位置，然后根节点下沉。下沉之后，该节点继续和左右孩子相比，直到左右孩子都大于或等于该节点才结束。

**构建二叉堆。**所谓构建，就是给你一个有n个节点的**无序的完全二叉树**，然后把它构建成二叉堆。要把一个无序的完全二叉树调整为二叉堆，我们可以让**所有非叶子节点依次下沉**。不过下沉的顺序不是从根节点开始下沉，而是从下面的**非叶子节点**开始下沉，再依次往上。

### 希尔排序

![img](https://pics7.baidu.com/feed/bba1cd11728b471067f07adaa69211f6fd0323b5.jpeg@f_auto?token=b1f33ccfabef23688ed7a985e709fe68)

### 桶排序

桶排序就是把最大值和最小值之间的数进行瓜分，例如分成 10 个区间，10个区间对应10个桶，我们把各元素放到对应区间的桶中去，再对每个桶中的数进行排序，可以采用归并排序，也可以采用快速排序之类的。之后每个桶里面的数据就是有序的了，再合并汇总即可。

![img](https://pics3.baidu.com/feed/8326cffc1e178a82a3f1f318ab5fa186a877e825.jpeg@f_auto?token=2a04d617c85396f4f06d2c45533a1e72)

### 归并排序+逆序对计数

归并排序的步骤：

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

# 2.树

**层级 Level**： 从根节点开始到达一个节点的路径，所包含的**边的数量**，称为这个节点的层级。 

**高度 Height**：树中所有节点的最大层级称为树的高度。

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

二叉堆本质上是一种完全二叉树，它分为两类：**最大堆和最小堆**。最大堆的任何一个父节点的值都大于或等于它左右孩子节点的值；最小堆的任何一个父节点的值，都小于或等于它左右孩子节点的值。二叉堆的入队操作和出队操作均可达到 O(logn) 。

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

### 二叉排序树

二叉排序树是一种特殊的二叉树，它具有以下性质：

1. 对于树中的每个节点，其左子树中的所有节点的关键码值小于该节点的关键码值。
2. 对于树中的每个节点，其右子树中的所有节点的关键码值大于该节点的关键码值。
3. 左右子树也是二叉排序树。

在构建二叉排序树时，我们按照以下原理：

- 从根节点开始，依次遍历关键码集合中的每个关键码。
- 对于每个关键码，如果树为空，则将关键码作为树的根节点。如果树不为空，则按照二叉排序树的性质，找到适当的位置插入关键码，以保持二叉排序树的性质不变。

具体来说，在插入关键码时，我们从根节点开始，逐级比较关键码值，并根据比较结果决定向左子树或右子树移动，直到找到合适的插入位置。如果待插入的关键码已经存在于树中，则不执行任何操作（可以视为树中已经存在该关键码）。

这样，通过按照关键码集合中记录的顺序依次插入节点，就可以构建出一个二叉排序树。

**查询效率最高的最优二叉排序树是AVL树**

例题：关键码集合为{18，73，5，10，68，99，27}

 二叉排序树构建过程如下：

```
           18
          /  \
        5     73
         \    / \
         10  68 99
              /
            27
```

### AVL树（平衡二叉搜索树）

平衡因子：左右子树的高度之差

我们认为，保证树的平衡因子为-1、0或1，可以使关键操作获得更好的大 O 性能。首先考虑平衡因子如何改善最坏情况。有左倾与右倾这两种可能性。

最不平衡的左倾树：

![image-20240616190213931](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240616190213931.png)

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

### Huffman树，Huffman算法

带权路径长度（WPL）最小的二叉树称作哈夫曼树。

这段代码首先定义了一个 `Node` 类来表示哈夫曼树的节点。然后，使用最小堆来构建哈夫曼树，每次从堆中取出两个频率最小的节点进行合并，直到堆中只剩下一个节点，即哈夫曼树的根节点。接着，使用递归方法计算哈夫曼树的带权外部路径长度（weighted external path length）。最后，输出计算得到的带权外部路径长度。

例题：用 Huffman 算法构造一个最优二叉编码树，待编码的字符权值分别为{3，4，5，6，8，9，11，12}，请问该最优二叉编码树的带权外部路径长度为（ B ）。（补充说明：树的带权外部路径长度定义为树中所有叶子结点的带权路径长度之和；其中，结点的带权路径长度定义为该结点到树根之间的路径长度与该结点权值的乘积）（ B ）
A：58	**B：169**	C：72	D：18

#为了构造哈夫曼树，我们遵循一个重复的选择过程，每次选择两个最小的权值创建一个新的节点，直到只剩下一个节点为止。我们可以按照以下步骤操作：

1. 将给定的权值排序：{3, 4, 5, 6, 8, 9, 11, 12}。

2. 选择两个最小的权值：3 和 4，将它们组合成一个新的权值为 7 的节点。

   现在权值变为：{5, 6, 7, 8, 9, 11, 12}。

3. 再次选择两个最小的权值：5 和 6，将它们组合成一个新的权值为 11 的节点。

   现在权值变为：{7, 8, 9, 11, 11, 12}。

4. 选择两个最小的权值：7 和 8，将它们组合成一个新的权值为 15 的节点。

   现在权值变为：{9, 11, 11, 12, 15}。

5. 选择两个最小的权值：9 和 11，将它们合并成一个新的权值为 20 的节点。

   现在权值变为：{11, 12, 15, 20}。

6. 选择两个最小的权值：11 和 12，合并成一个新的权值为 23 的节点。

   现在权值变为：{15, 20, 23}。

7. 选择两个最小的权值：15 和 20，合并成一个新的权值为 35 的节点。

   现在权值变为：{23, 35}。

8. 最后，合并这两个节点得到根节点，权值为 23 + 35 = 58。

现在我们可以计算哈夫曼树的带权外部路径长度（WPL）。

```
          (58)
        /      \
     (23)       (35)
     /  \       /   \
   (11)(12)  (20)    (15) 
             / \       / \
            (9)(11)   (7)(8)
                / \   / \  
               (5)(6)(3) (4)
```

现在让我们计算每个叶子节点的带权路径长度：

- 权值 3 的节点路径长度为 4，WPL部分为 3 * 4 = 12。
- 权值 4 的节点路径长度为 4，WPL部分为 4 * 4 = 16。
- 权值 5 的节点路径长度为 4，WPL部分为 5 * 4 = 20。
- 权值 6 的节点路径长度为 4，WPL部分为 6 * 4 = 24。
- 权值 9 的节点路径长度为 3，WPL部分为 9 * 3 = 27。
- 权值 8 的节点路径长度为 3，WPL部分为 8 * 3 = 24。
- 权值 11 的节点路径长度为 2，WPL部分为 11 * 2 = 22。
- 权值 12 的节点路径长度为 2，WPL部分为 12 * 2 = 24。

将所有部分的 WPL 相加，我们得到整棵哈夫曼树的 WPL：

WPL = 12 + 16 + 20 + 24 + 27 + 24 + 22 + 24 = 169

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

 Huffman’s algorithm constructs an optimal prefix code for a string of length n with d distinct characters in **𝑂(𝑛+𝑑𝑙𝑜𝑔𝑑) time.**

![image-20240618113101645](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240618113101645.png)

# 散列表

直接由关键字找到相应的记录。这就是散**列查找法**（Hash Search）的思想，它通过对元素的关键字值进行某种运算，直接求出元素的地址，即使用关键字到地址的直接转换方法，而不需要反复比较。

下面给出散列法中常用的几个术语。

(1) **散列函数和散列地址**：在记录的存储位置p和其关键字 key 之间建立一个确定的对应关系 H，使p=H(key)，称这个对应关系H为散列函数，p为散列地址。

(2) **散列表**：一个有限连续的地址空间，用以存储按散列函数计算得到相应散列地址的数据记录。通常散列表的存储空间是一个一维数组，散列地址是数组的下标。

(3) **冲突和同义词**：对不同的关键字可能得到同一散列地址,即 key1≠key2,而 H(key1) = H(key2) 这种现象称为冲突。具有相同函数值的关键字对该散列函数来说称作同义词，key1与 key2 互称为同义词。

### 处理冲突的方法

选择一个“好”的散列函数可以在一定程度上减少冲突，但在实际应用中，很难完全避免发生冲突，所以选择一个有效的处理冲突的方法是散列法的另一个关键问题。创建散列表和查找散列表都会遇到冲突，两种情况下处理冲突的方法应该一致。下面以创建散列表为例，来说明处理冲突的方法。

处理冲突的方法与散列表本身的组织形式有关。按组织形式的不同，通常分两大类:开放地址法和链地址法。

#### 开放地址法（闭散列法）

开放地址法的基本思想是：把记录都存储在散列表数组中，当某一记录关键字 key 的初始散列地址 H0 = H(key)发生冲突时，以 H0 为基础，采取合适方法计算得到另一个地址 H1，如果 H1 仍然发生冲突，以 H1 为基础再求下一个地址 H2，若 H2 仍然冲突，再求得 H3。依次类推，直至 Hk 不发生冲突为止，则 Hk 为该记录在表中的散列地址。

这种方法在寻找 ”下一个” 空的散列地址时，原来的数组空间对所有的元素都是开放的所以称为开放地址法。通常把寻找 “下一个” 空位的过程称为**探测**，上述方法可用如下公式表示：

Hi=(H(key) +di)%m i=1,2,…,k(k≤m-l)

其中，H(key)为散列函数，m 为散列表表长，d为增量序列。根据d取值的不同，可以分为以下3种探测方法。

(1) 线性探测法 di = 1, 2, 3, …, m-1

这种探测方法可以将散列表假想成一个循环表，发生冲突时，从冲突地址的下一单元顺序寻找空单元，如果到最后一个位置也没找到空单元，则回到表头开始继续查找，直到找到一个空位，就把此元素放入此空位中。如果找不到空位，则说明散列表已满，需要进行溢出处理。

(2) 二次探测法 𝑑𝑖=1,−1,4,−4,9,.…,+𝑘^2,−𝑘^2 (𝑘≤𝑚/2)

(3)伪随机探测法

di = 伪随机数序列的对应数

例如，散列表的长度为 11，散列函数 H(key)=key%11，假设表中已填有关键字分别为 17、60、29 的记录，如图7.29(a)所示。现有第四个记录，其关键字为38，由散列函数得到散列地址为 5，产生冲突。

若用线性探测法处理时，得到下一个地址6，仍冲突；再求下一个地址7，仍冲突；直到散列地址为8的位置为“空”时为止，处理冲突的过程结束，38填入散列表中序号为8的位置，如图2(b)所示。

若用二次探测法，散列地址5冲突后，得到下一个地址5+1=6，仍冲突；再求得下一个地址5-1= 4，无冲突，38填入序号为4的位置，如图 2(c)所示。

若用伪随机探测法，假设产生的伪随机数为9，则计算下一个散列地址为(5+9)%11=3，所以38 填入序号为3 的位置，如图 2(d)所示。

![image-20240618101439573](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240618101439573.png)

# 图

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

**图的类实现（笔试）**

Graph类存储包含所有顶点的主列表，Vertex类表示图中的每一个顶点。 Vertex使用字典connectedTo来记录与其相连的顶点，以及每一条边的权重。代码清单7-1展示了Vertex类的实现，其构造方法简单地初始化id（它通常是一个字符串），以及字典connectedTo。addNeighbor方法添加从一个顶点到另一个的连接。getConnections方法返回邻接表中的所有顶点，由connectedTo来表示。getWeight方法返回从当前顶点到以参数传入的顶点之间的边的权重。

```python
class Vertex:
    def __init__(self,key):
        self.id = key
        self.connectedTo = {}
    def addNeighbor(self,nbr,weight=0):
        self.connectedTo[nbr] = weight
    def __str__(self):
        return str(self.id) + ' connectedTo: ' + str([x.id for x in self.connectedTo])
    def getConnections(self):
        return self.connectedTo.keys()
    def getId(self):
        return self.id
    def getWeight(self,nbr):
        return self.connectedTo[nbr]

class Graph:
    def __init__(self):
        self.vertList = {} #将顶点名映射到顶点对象的字典
        self.numVertices = 0
    def addVertex(self,key):
        self.numVertices = self.numVertices + 1
        newVertex = Vertex(key)
        self.vertList[key] = newVertex
        return newVertex
    def getVertex(self,n): #返回图中所有顶点的名字
        if n in self.vertList:
            return self.vertList[n]
        else:
            return None
    def __contains__(self,n):
        return n in self.vertList
    def addEdge(self,f,t,weight=0):
        if f not in self.vertList:
            nv = self.addVertex(f)
        if t not in self.vertList:
            nv = self.addVertex(t)
        self.vertList[f].addNeighbor(self.vertList[t], weight)
    def getVertices(self):
        return self.vertList.keys()
    def __iter__(self):
        return iter(self.vertList.values())
```

**邻接表**

为了实现稀疏连接的图，更高效的方式是使用**邻接表**。在邻接表实现中，我们为图对象的所有顶点保存一个主列表，同时为每一个顶点对象都维护一个列表，其中记录了与它相连的顶点。在对Vertex类的实现中，我们使用字典（而不是列表），字典的键是顶点，值是权重。

![image-20240616200120278](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240616200120278.png)

无向图的邻接表：

![image-20240618100445273](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240618100445273.png)

有向图的邻接表：

![image-20240618100554745](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240618100554745.png)

![image-20240618100620983](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240618100620983.png)

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

用类实现词梯问题（bfs）：

```python
import sys
from collections import deque
class Graph:
    def __init__(self):
        self.vertices = {}
        self.num_vertices = 0
    def add_vertex(self, key):
        self.num_vertices = self.num_vertices + 1
        new_vertex = Vertex(key)
        self.vertices[key] = new_vertex
        return new_vertex
    def get_vertex(self, n):
        if n in self.vertices:
            return self.vertices[n]
        else:
            return None
    def __len__(self):
        return self.num_vertices
    def __contains__(self, n):
        return n in self.vertices
    def add_edge(self, f, t, cost=0):
        if f not in self.vertices:
            nv = self.add_vertex(f)
        if t not in self.vertices:
            nv = self.add_vertex(t)
        self.vertices[f].add_neighbor(self.vertices[t], cost)
    def get_vertices(self):
        return list(self.vertices.keys())
    def __iter__(self):
        return iter(self.vertices.values())

class Vertex:
    def __init__(self, num):
        self.key = num
        self.connectedTo = {}
        self.color = 'white'
        self.distance = sys.maxsize
        self.previous = None
        self.disc = 0
        self.fin = 0
    def add_neighbor(self, nbr, weight=0):
        self.connectedTo[nbr] = weight
    def get_neighbors(self):
        return self.connectedTo.keys()

def build_graph(filename):
    buckets = {}
    the_graph = Graph()
    with open(filename, "r", encoding="utf8") as file_in:
        all_words = file_in.readlines()
    for line in all_words:
        word = line.strip()
        for i, _ in enumerate(word):
            bucket = f"{word[:i]}_{word[i + 1:]}"
            buckets.setdefault(bucket, set()).add(word)

    # connect different words in the same bucket
    for similar_words in buckets.values():
        for word1 in similar_words:
            for word2 in similar_words - {word1}:
                the_graph.add_edge(word1, word2)
    return the_graph

def bfs(start):
    start.distnce = 0
    start.previous = None
    vert_queue = deque()
    vert_queue.append(start)
    while len(vert_queue) > 0:
        current = vert_queue.popleft()  # 取队首作为当前顶点
        for neighbor in current.get_neighbors():   # 遍历当前顶点的邻接顶点
            if neighbor.color == "white":
                neighbor.color = "gray"
                neighbor.distance = current.distance + 1
                neighbor.previous = current
                vert_queue.append(neighbor)
        current.color = "black" # 当前顶点已经处理完毕，设黑色
"""
BFS 算法主体是两个循环的嵌套: while-for
    while 循环对图中每个顶点访问一次，所以是 O(|V|)；
    嵌套在 while 中的 for，由于每条边只有在其起始顶点u出队的时候才会被检查一次，
    而每个顶点最多出队1次，所以边最多被检查次，一共是 O(|E|)；
    综合起来 BFS 的时间复杂度为 0(V+|E|)

词梯问题还包括两个部分算法
    建立 BFS 树之后，回溯顶点到起始顶点的过程，最多为 O(|V|)
    创建单词关系图也需要时间，时间是 O(|V|+|E|) 的，因为每个顶点和边都只被处理一次
"""
# 回溯路径
def traverse(starting_vertex):
    ans = []
    current = starting_vertex
    while (current.previous):
        ans.append(current.key)
        current = current.previous
    ans.append(current.key)

    return ans

g = build_graph("vocabulary.txt")
print(len(g))
bfs(g.get_vertex("FOOL"))
ans = traverse(g.get_vertex("SAGE")) # 从SAGE开始回溯，逆向打印路径，直到FOOL
print(*ans[::-1])
"""
3867
FOOL TOOL TOLL TALL SALL SALE SAGE
"""
```

用类实现骑士周游问题（Warnsdorff算法）

```python
import sys
class Graph:
    def __init__(self):
        self.vertices = {}
        self.num_vertices = 0
    def add_vertex(self, key):
        self.num_vertices = self.num_vertices + 1
        new_ertex = Vertex(key)
        self.vertices[key] = new_ertex
        return new_ertex
    def get_vertex(self, n):
        if n in self.vertices:
            return self.vertices[n]
        else:
            return None
    def __len__(self):
        return self.num_vertices
    def __contains__(self, n):
        return n in self.vertices
    def add_edge(self, f, t, cost=0):
        if f not in self.vertices:
            nv = self.add_vertex(f)
        if t not in self.vertices:
            nv = self.add_vertex(t)
        self.vertices[f].add_neighbor(self.vertices[t], cost)
    def getVertices(self):
        return list(self.vertices.keys())
    def __iter__(self):
        return iter(self.vertices.values())


class Vertex:
    def __init__(self, num):
        self.key = num
        self.connectedTo = {}
        self.color = 'white'
        self.distance = sys.maxsize
        self.previous = None
        self.disc = 0
        self.fin = 0
    def __lt__(self,o):
        return self.key < o.key
    def add_neighbor(self, nbr, weight=0):
        self.connectedTo[nbr] = weight
    def get_neighbors(self):
        return self.connectedTo.keys()
    def __str__(self):
        return str(self.key) + ":color " + self.color + ":disc " + str(self.disc) + ":fin " + str(
            self.fin) + ":dist " + str(self.distance) + ":pred \n\t[" + str(self.previous) + "]\n"

def knight_graph(board_size):
    kt_graph = Graph()
    for row in range(board_size):           #遍历每一行
        for col in range(board_size):       #遍历行上的每一个格子
            node_id = pos_to_node_id(row, col, board_size) #把行、列号转为格子ID
            new_positions = gen_legal_moves(row, col, board_size) #按照 马走日，返回下一步可能位置
            for row2, col2 in new_positions:
                other_node_id = pos_to_node_id(row2, col2, board_size) #下一步的格子ID
                kt_graph.add_edge(node_id, other_node_id) #在骑士周游图中为两个格子加一条边
    return kt_graph

def pos_to_node_id(x, y, bdSize):
    return x * bdSize + y

def gen_legal_moves(row, col, board_size):
    new_moves = []
    move_offsets = [(-1, -2), (-1, 2), (-2, -1), (-2, 1), (1, -2), (1, 2), (2, -1), (2, 1)] #马走日的8种走法
    for r_off, c_off in move_offsets:
        if (0 <= row + r_off < board_size and 0 <= col + c_off < board_size):#检查，不能走出棋盘
            new_moves.append((row + r_off, col + c_off))
    return new_moves

def knight_tour(n, path, u, limit):
    u.color = "gray"
    path.append(u)              #当前顶点涂色并加入路径
    if n < limit:
        neighbors = ordered_by_avail(u) #对所有的合法移动依次深入
        i = 0
        for nbr in neighbors:
            if nbr.color == "white" and knight_tour(n + 1, path, nbr, limit): 
                return True #选择“白色”未经深入的点，层次加一，递归深入
        else: #所有的“下一步”都试了走不通
            path.pop()              #回溯，从路径中删除当前顶点
            u.color = "white"       #当前顶点改回白色
            return False
    else:
        return True

def ordered_by_avail(n):
    res_list = []
    for v in n.get_neighbors():
        if v.color == "white":
            c = 0
            for w in v.get_neighbors():
                if w.color == "white":
                    c += 1
            res_list.append((c,v))
    res_list.sort(key = lambda x: x[0])
    return [y[1] for y in res_list]

def main():
    def NodeToPos(id):
       return ((id//8, id%8))
    bdSize = int(input())  # 棋盘大小
    *start_pos, = map(int, input().split())  # 起始位置
    g = knight_graph(bdSize)
    start_vertex = g.get_vertex(pos_to_node_id(start_pos[0], start_pos[1], bdSize))
    if start_vertex is None:
        print("fail")
        exit(0)
    tour_path = []
    done = knight_tour(0, tour_path, start_vertex, bdSize * bdSize-1)
    if done:
        print("success")
    else:
        print("fail")
    exit(0)
    # 打印路径
    cnt = 0
    for vertex in tour_path:
        cnt += 1
        if cnt % bdSize == 0:
            print()
        else:
            print(vertex.key, end=" ")
if __name__ == '__main__':
    main()
```

**通用深度优先搜索**

有时候深度优先搜索会创建多棵深度优先搜索树，称之为**深度优先森林（Depth First Forest）**。和宽度优先搜索类似，深度优先搜索也利用前驱连接来构建树。此外，深度优先搜索还会使用Vertex类中的两个额外的实例变量：`发现时间`记录算法在第一次访问顶点时的步数，`结束时间`记录算法在顶点被标记为黑色时的步数。在学习之后会发现，顶点的`发现时间`和`结束时间`提供了一些有趣的特性，后续算法会用到这些特性。

由于`dfs函数`和`dfsvisit`辅助函数使用一个变量来记录调用`dfsvisit`的时间，因此我们选择将代码作为Graph类的一个子类中的方法来实现。该实现继承Graph类，并且增加了time实例变量，以及dfs和dfsvisit两个方法。注意第10行，dfs方法遍历图中所有的顶点，并对白色顶点调用`dfsvisit`方法。之所以遍历所有的顶点，而不是简单地从一个指定的顶点开始搜索，是因为这样做能够确保深度优先森林中的所有顶点都在考虑范围内，而不会有被遗漏的顶点。`for vertex in self`这条语句可能看上去不太正确，但是此处的self是DFSGraph类的一个实例，遍历一个图实例中的所有顶点其实是一件非常自然的事情。

从`start_vertex`开始，`dfs_visit`方法尽可能深地探索所有相邻的白色顶点。如果仔细观察`df_svisit`的代码并且将其与bfs比较，应该注意到二者几乎一样，除了内部for循环的最后一行，`dfs_visit`通过递归地调用自己来继续进行下一层的搜索，bfs则将顶点添加到队列中，以供后续搜索。有趣的是，bfs使用队列，dfsvisit则使用栈。我们没有在代码中看到栈，但是它其实隐式地存在于`dfs_visit`的递归调用中。

图7展示了在小型图上应用深度优先搜索算法的过程。图中，虚线表示被检查过的边，但是其一端的顶点已经被添加到深度优先搜索树中。在代码中，这是通过检查另一端的顶点是否不为白色来完成的。

```python
from pythonds3.graphs import Graph

class DFSGraph(Graph):
    def __init__(self):
        super().__init__()
        self.time = 0                   #不是物理世界，而是算法执行步数

    def dfs(self):
        for vertex in self:
            vertex.color = "white"      #颜色初始化
            vertex.previous = -1
        for vertex in self:             #从每个顶点开始遍历
            if vertex.color == "white":
                self.dfs_visit(vertex)  #第一次运行后还有未包括的顶点
                                        # 则建立森林

    def dfs_visit(self, start_vertex):
        start_vertex.color = "gray"
        self.time = self.time + 1       #记录算法的步骤
        start_vertex.discovery_time = self.time
        for next_vertex in start_vertex.get_neighbors():
            if next_vertex.color == "white":
                next_vertex.previous = start_vertex
                self.dfs_visit(next_vertex)     #深度优先递归访问
        start_vertex.color = "black"
        self.time = self.time + 1
        start_vertex.closing_time = self.time
```

![1718540349831](E:\wechat files\WeChat Files\wxid_33sx10zfgdi122\FileStorage\Temp\1718540349831.png)

搜索从图中的顶点A开始。由于所有顶点一开始都是白色的，因此算法会访问A。访问顶点的第一步是将其颜色设置为灰色，以表明正在访问该顶点，并将其发现时间设为1。由于A有两个相邻顶点（B和D），因此它们都需要被访问。我们按照字母顺序来访问顶点。

接下来访问顶点B，将它的颜色设置为灰色，并把发现时间设置为2。B也与两个顶点（C和D）相邻，因此根据字母顺序访问C。 访问C时，搜索到达某个分支的终点。在将C标为灰色并且把发现时间设置为3之后，算法发现C没有相邻顶点。这意味着对C的探索完成，因此将它标为黑色，并将完成时间设置为4。图7d展示了搜索至这一步时的状态。

由于C是一个分支的终点，因此需要返回到B，并且继续探索其余的相邻顶点。唯一的待探索顶点就是D，它把搜索引到E。E有两个相邻顶点，即B和F。正常情况下，应该按照字母顺序来访问这两个顶点，但是由于B已经被标记为灰色，因此算法自知不应该访问B，因为如果这么做就会陷入死循环。因此，探索过程跳过B，继续访问F。

F只有C这一个相邻顶点，但是C已经被标记为黑色，因此没有后续顶点需要探索，也即到达另一个分支的终点。从此时起，算法一路回溯到起点，同时为各个顶点设置完成时间并将它们标记为黑色，如图7h～图7l所示。

每个顶点的发现时间和结束时间都体现了括号特性，这意味着深度优先搜索树中的任一节点的子节点都有比该节点更晚的发现时间和更早的结束时间。以下是通过深度优先搜索算法构建的树。

![image-20240616202015261](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20240616202015261.png)

一般来说，深度优先搜索的运行时间如下。在`dfs函数`中有两个循环，每个都是|V|次，所以是O(|V|)，这是由于它们针对图中的每个顶点都只执行一次。在`dfs_visit`中，循环针对当前顶点的邻接表中的每一条边都执行一次，且仅在顶点是白色时被递归调用，因此循环最多会对图中的每一条边执行一次，也就是O(|E|)。因此，深度优先搜索算法的时间复杂度是O(|V|+|E|)，与BFS一样。

*深度优先搜索树（林）的性质

1. **顶点大小的定义：** 对于图 G 中的任意两个顶点 u 和 v，当且仅当顶点 u 的结束时间（fin）小于顶点 v 的结束时间（fin）时，称顶点 u 小于顶点 v。
2. **子树大小的定义：** 对于深度优先搜索树（林）中的任意两棵不相交的子树 t1 和 t2，当且仅当 t1 中的任意节点的结束时间都早于 t2 中的任意节点的开始时间时，称 t1 小于 t2。
3. **节点间的互斥关系：** 如果 t1 小于 t2，且节点 u 属于 t1，节点 v 属于 t2，则 𝑢→𝑣 不存在。
4. **节点间的关系限定：** 如果顶点 u 小于顶点 v，并且它们在同一棵树中，则只有两种可能情况：
   - 顶点 v 是顶点 u 的祖先。
   - 顶点 u 和 v 具有共同的祖先 t。其中，顶点 u 属于 t 的子树 t1，顶点 v 属于 t 的子树 t2。这种情况下，t1 的结束时间早于 t2 的开始时间。

**强连通单元**

有向图G**强连通**是指G中任意两点强连通, 即这两个点从其一到另一都有路径. **强连通分量** 是指极大的强连通子图. 

无向图G**连通**是指G 中任意两点连通, 即这两点之间有路径. **连通分量**是指极大的连通子图.

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

**Kahn算法**是基于广度优先搜索（BFS）的一种拓扑排序算法。步骤：

1. 初始化一个队列，用于存储当前入度为0的顶点。
2. 遍历图中的所有顶点，计算每个顶点的入度，并将入度为0的顶点加入到队列中。
3. 不断地从队列中弹出顶点，并将其加入到拓扑排序的结果中。同时，遍历该顶点的邻居，并将其入度减1。如果某个邻居的入度减为0，则将其加入到队列中。
4. 重复步骤3，直到队列为空。

```python
from collections import deque, defaultdict

def topological_sort(graph):
    indegree = defaultdict(int)
    result = []
    queue = deque()
    # 计算每个顶点的入度
    for u in graph:
        for v in graph[u]:
            indegree[v] += 1
    # 将入度为 0 的顶点加入队列
    for u in graph:
        if indegree[u] == 0:
            queue.append(u)
    # 执行拓扑排序
    while queue:
        u = queue.popleft()
        result.append(u)

        for v in graph[u]:
            indegree[v] -= 1
            if indegree[v] == 0:
                queue.append(v)
    # 检查是否存在环
    if len(result) == len(graph):
        return result
    else:
        return None

    # 示例调用代码
graph = {
    'A': ['B', 'C'],
    'B': ['C', 'D'],
    'C': ['E'],
    'D': ['F'],
    'E': ['F'],
    'F': []
}
sorted_vertices = topological_sort(graph)
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

```python
import heapq
import sys
class Vertex:
    def __init__(self, key):
        self.id = key
        self.connectedTo = {}
        self.distance = sys.maxsize
        self.pred = None
    def addNeighbor(self, nbr, weight=0):
        self.connectedTo[nbr] = weight
    def getConnections(self):
        return self.connectedTo.keys()
    def getWeight(self, nbr):
        return self.connectedTo[nbr]
    def __lt__(self, other):
        return self.distance < other.distance

class Graph:
    def __init__(self):
        self.vertList = {}
        self.numVertices = 0
    def addVertex(self, key):
        newVertex = Vertex(key)
        self.vertList[key] = newVertex
        self.numVertices += 1
        return newVertex
    def getVertex(self, n):
        return self.vertList.get(n)
    def addEdge(self, f, t, cost=0):
        if f not in self.vertList:
            self.addVertex(f)
        if t not in self.vertList:
            self.addVertex(t)
        self.vertList[f].addNeighbor(self.vertList[t], cost)

def dijkstra(graph, start):
    pq = []
    start.distance = 0
    heapq.heappush(pq, (0, start))
    visited = set()
    while pq:
        currentDist, currentVert = heapq.heappop(pq)    # 当一个顶点的最短路径确定后（也就是这个顶点
                                                        # 从优先队列中被弹出时），它的最短路径不会再改变。
        if currentVert in visited:
            continue
        visited.add(currentVert)
        for nextVert in currentVert.getConnections():
            newDist = currentDist + currentVert.getWeight(nextVert)
            if newDist < nextVert.distance:
                nextVert.distance = newDist
                nextVert.pred = currentVert
                heapq.heappush(pq, (newDist, nextVert))

g = Graph()
g.addEdge('A', 'B', 4)
g.addEdge('A', 'C', 2)
g.addEdge('C', 'B', 1)
g.addEdge('B', 'D', 2)
g.addEdge('C', 'D', 5)
g.addEdge('D', 'E', 3)
g.addEdge('E', 'F', 1)
g.addEdge('D', 'F', 6)
print("Shortest Path Tree:")
dijkstra(g, g.getVertex('A'))
# 输出最短路径树的顶点及其距离
for vertex in g.vertList.values():
    print(f"Vertex: {vertex.id}, Distance: {vertex.distance}")
# 输出最短路径到每个顶点
def printPath(vert):
    if vert.pred:
        printPath(vert.pred)
        print(" -> ", end="")
    print(vert.id, end="")
print("\nPaths from Start Vertex 'A':")
for vertex in g.vertList.values():
    print(f"Path to {vertex.id}: ", end="")
    printPath(vertex)
    print(", Distance: ", vertex.distance)
"""
Shortest Path Tree:
Vertex: A, Distance: 0
Vertex: B, Distance: 3
Vertex: C, Distance: 2
Vertex: D, Distance: 5
Vertex: E, Distance: 8
Vertex: F, Distance: 9

Paths from Start Vertex 'A':
Path to A: A, Distance:  0
Path to B: A -> C -> B, Distance:  3
Path to C: A -> C, Distance:  2
Path to D: A -> C -> B -> D, Distance:  5
Path to E: A -> C -> B -> D -> E, Distance:  8
Path to F: A -> C -> B -> D -> E -> F, Distance:  9
"""
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
##
class DisjointSet:
    def __init__(self, num_vertices):
        self.parent = list(range(num_vertices))
        self.rank = [0] * num_vertices

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, x, y):
        root_x = self.find(x)
        root_y = self.find(y)

        if root_x != root_y:
            if self.rank[root_x] < self.rank[root_y]:
                self.parent[root_x] = root_y
            elif self.rank[root_x] > self.rank[root_y]:
                self.parent[root_y] = root_x
            else:
                self.parent[root_x] = root_y
                self.rank[root_y] += 1

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

```python
import sys
import heapq
class Vertex:
    def __init__(self, key):
        self.id = key
        self.connectedTo = {}
        self.distance = sys.maxsize
        self.pred = None
    def addNeighbor(self, nbr, weight=0):
        self.connectedTo[nbr] = weight
    def getConnections(self):
        return self.connectedTo.keys()
    def getWeight(self, nbr):
        return self.connectedTo[nbr]
    def __lt__(self, other):
        return self.distance < other.distance

class Graph:
    def __init__(self):
        self.vertList = {}
        self.numVertices = 0
    def addVertex(self, key):
        newVertex = Vertex(key)
        self.vertList[key] = newVertex
        self.numVertices += 1
        return newVertex
    def getVertex(self, n):
        return self.vertList.get(n)
    def addEdge(self, f, t, cost=0):
        if f not in self.vertList:
            self.addVertex(f)
        if t not in self.vertList:
            self.addVertex(t)
        self.vertList[f].addNeighbor(self.vertList[t], cost)
        self.vertList[t].addNeighbor(self.vertList[f], cost)

def prim(graph, start):
    pq = []
    start.distance = 0
    heapq.heappush(pq, (0, start))
    visited = set()
    while pq:
        currentDist, currentVert = heapq.heappop(pq)
        if currentVert in visited:
            continue
        visited.add(currentVert)
        for nextVert in currentVert.getConnections():
            weight = currentVert.getWeight(nextVert)
            if nextVert not in visited and weight < nextVert.distance:
                nextVert.distance = weight
                nextVert.pred = currentVert
                heapq.heappush(pq, (weight, nextVert))

# 创建图和边
g = Graph()
g.addEdge('A', 'B', 4)
g.addEdge('A', 'C', 3)
g.addEdge('C', 'B', 1)
g.addEdge('C', 'D', 2)
g.addEdge('D', 'B', 5)
g.addEdge('D', 'E', 6)
print("Minimum Spanning Tree:")
prim(g, g.getVertex('A'))
# 输出最小生成树的边
for vertex in g.vertList.values():
    if vertex.pred:
        print(f"{vertex.pred.id} -> {vertex.id} Weight:{vertex.distance}")
"""
Minimum Spanning Tree:
C -> B Weight:1
A -> C Weight:3
C -> D Weight:2
D -> E Weight:6
"""
```

## 绪论（5个题）

（1）在数据结构中，从逻辑上可以把数据结构分成（ C  ）。

A．动态结构和静态结构   B．紧凑结构和非紧凑结构

C．线性结构和非线性结构  D．内部结构和外部结构

（2）与数据元素本身的形式、内容、相对位置、个数无关的是数据的（ C ）。

A．存储结构        B．存储实现

C．逻辑结构        D．运算实现

（3）通常要求同一逻辑结构中的所有数据元素具有相同的特性，这意味着( B )。

A．数据具有同一特点

B．不仅数据元素所包含的数据项的个数要相同，而且对应数据项的类型要一致

C．每个数据元素都一样

D．数据元素所包含的数据项的个数要相等

（4）以下说法正确的是（ D ）。

A．数据元素是数据的最小单位

B．数据项是数据的基本单位

C．数据结构是带有结构的各数据项的集合

D．一些表面上很不相同的数据可以有相同的逻辑结构

（5）以下与数据的存储结构无关的术语是（ C ）。

A．顺序队列    B. 链表    C. 有序表     D. 链栈

## 线性表（14个题）

（1）在n个结点的顺序表中，算法的时间复杂度是O(1)的操作是（ A ）

A．访问第i个结点（1≤i≤n）和求第i个结点的直接前驱（2≤i≤n） 

B．在第i个结点后插入一个新结点（1≤i≤n）

C．删除第i个结点（1≤i≤n）

D．将n个结点从小到大排序

（2）向一个有127个元素的顺序表中插入一个新元素并保持原来顺序不变，平均要移动 的元素个数为（ B ）。
A．8   B．63.5    C．63   D．7

（3）链接存储的存储结构所占存储空间（ A ）。

A．分两部分，一部分存放结点值，另一部分存放表示结点间关系的指针

B．只有一部分，存放结点值

C．只有一部分，存储表示结点间关系的指针

D．分两部分，一部分存放结点值，另一部分存放结点所占单元数

（4）线性表若采用链式存储结构时，要求内存中可用存储单元的地址（ D ）。

A．必须是连续的    B．部分地址必须是连续的

C．一定是不连续的   D．连续或不连续都可以

（5）线性表Ｌ在（ B  ）情况下适用于使用链式结构实现。

A．需经常修改Ｌ中的结点值   Ｂ．需不断对Ｌ进行删除插入 

C．Ｌ中含有大量的结点     Ｄ．Ｌ中结点结构复杂

（6）单链表的存储密度（ C ）。

A．大于1    B．等于1   C．小于1  D．不能确定

（7）将两个各有n个元素的有序表归并成一个有序表，其最少的比较次数是（ A ）。

A．n       B．2n-1     C．2n     D．n-1

（8）在一个长度为n的顺序表中，在第i个元素（1≤i≤n+1）之前插入一个新元素时须向后移动（ B ）个元素。

A．n-i      B．n-i+1    C．n-i-1    D．i

（9）线性表L=(a1，a2,……an)，下列说法正确的是（ D ）。

A．每个元素都有一个直接前驱和一个直接后继

B．线性表中至少有一个元素

C．表中诸元素的排列必须是由小到大或由大到小

D．除第一个和最后一个元素外，其余每个元素都有一个且仅有一个直接前驱和直接后继。

（10）若指定有n个元素的向量，则建立一个有序单链表的时间复杂性的量级是（ C ）。

A．O(1)      B．O(n)       C．O(n^2)      D．O(nlog_2n)

#建立一个有序单链表的时间复杂度是O(n^2)。这是因为在单链表中插入元素需要遍历链表找到正确的位置，而每次插入的时间复杂度是O(n)，如果需要插入n个元素，那么总的时间复杂度就是O(n^2)。

（11）以下说法错误的是（ D ）。

A．求表长、定位这两种运算在采用顺序存储结构时实现的效率不比采用链式存储结构时实现的效率低

B．顺序存储的线性表可以随机存取

C．由于顺序存储要求连续的存储区域，所以在存储管理上不够灵活

D．线性表的链式存储结构优于顺序存储结构

（12）在单链表中，要将s所指结点插入到p所指结点之后，其语句应为（ D ）。

A. s.next = p.next + 1; p.next = s;

B. p.next = s; s.next = p.next;

C. s.next = p.next; p.next = s.next;

D. s.next = p.next; p.next = s;

（13）在双向链表存储结构中，删除p所指的结点时须修改指针（ A ）。

A. p.next.prior = p.prior; p.prior.next = p.next;

B. p.next = p.next.next; p.next.prior = p;

C. p.prior.next = p; p.prior = p.prior.prior;

D. p.prior = p.next.next; p.next = p.prior.prior;

（14）在双向循环链表中，在p指针所指的结点后插入q所指向的新结点，其修改指针的操作是（ C ）。

A. p.next = q; q.prior = p; p.next.prior = q; q.next = q;

B. p.next = q; p.next.prior = q; q.prior = p; q.next = p.next;

C. q.prior = p; q.next = p.next; p.next.prior = q; p.next = q;

D. q.prior = p; q.next = p.next; p.next = q; p.next.prior = q;

## 树和二叉树（10个题）

（1）把一棵树转换为二叉树后，这棵二叉树的形态是（ A ）。       

A．唯一的              Ｂ．有多种

C．有多种，但根结点都没有左孩子  Ｄ．有多种，但根结点都没有右孩子

（2）由3 个结点可以构造出多少种不同的二叉树？（ D ）

A．2     B．3       C．4     D．5  

（3）一棵完全二叉树上有1001个结点，其中叶子结点的个数是（ D ）。

A．250     B． 500     C．254    D．501  

（4）一个具有1025个结点的二叉树的高h为（ D ）。

A．11     B．10       C．11至1025之间    D．10至1024之间

#有n个结点的非空完全二叉树的高度为⌈log2(n+1)⌉-1。即：有n个结点的非空完全二叉树共有⌈log2(n+1)⌉层结点。

（5）深度为h的满m叉树的第k层有（ A ）个结点。(1=<k=<h)

A．m^k-1^      B．m^k^-1      C．m^h-1^     D．m^h-1

（6）对二叉树的结点从1开始进行连续编号，要求每个结点的编号大于其左、右孩子的编号，同一结点的左右孩子中，其左孩子的编号小于其右孩子的编号，可采用（ C ）遍历实现编号。

A．先序     B. 中序      C. 后序    D. 从根开始按层次遍历

（7）若二叉树采用二叉链表存储结构，要交换其所有分支结点左、右子树的位置，利用（ C ）遍历方法最合适。

A．前序     B．中序       C．后序   D．按层次

（8）在下列存储形式中，（ D ）不是树的存储形式？

A．双亲表示法  B．孩子链表表示法  C．孩子兄弟表示法  D．顺序存储表示法

（9）一棵非空的二叉树的先序遍历序列与后序遍历序列正好相反，则该二叉树一定满足（ B ）。

A．所有的结点均无左孩子    B．所有的结点均无右孩子

C．只有一个叶子结点      D．是任意一棵二叉树

（10）设F是一个森林，B是由F变换得的二叉树。若F中有n个非终端结点，则B中右指针域为空的结点有（ C ）个。

A． n-1      B．n           C． n+1      D． n+2

## 查找（13个题）

（1）对n个元素的表做顺序查找时，若查找每个元素的概率相同，则平均查找长度为（ C ）。

A．(n-1)/2    B． n/2    C．(n+1)/2    D．n 

#对于顺序查找，如果每个元素被查找的概率相同，并且元素在表中均匀分布，那么平均查找长度（Average Search Length，ASL）可以通过元素在表中的位置的期望值来计算。

考虑在顺序查找中，每个元素被查找时，它可能在表中的位置分别为 1 到 n。因此，元素在表中的位置的期望值为：

期望位置 = (1 + 2 + 3 +…… + n)/n ={n+1}/2

因此，平均查找长度为(n+1)/2。所以答案是选项 C．

（2）适用于折半查找的表的存储方式及元素排列要求为（ D ）。

 A．链接方式存储，元素无序       B．链接方式存储，元素有序

C．顺序方式存储，元素无序      D．顺序方式存储，元素有序

（3）当在一个有序的顺序表上查找一个数据时，既可用折半查找，也可用顺序查找，但前者比后者的查找速度（ C ）。           

A．必定快              B．不一定  

C．在大部分情况下要快        D．取决于表递增还是递减

（4）折半查找有序表（4，6，10，12，20，30，50，70，88，100）。若查找表中元素58，则它将依次与表中（ A ）比较大小，查找结果是失败。

A．20，70，30，50          B．30，88，70，50 

C．20，50               D．30，88，50

（5）对22个记录的有序表作折半查找，当查找失败时，至少需要比较（ B ）次关键字。

A．3      B．4     C．5      D．6

（6）折半搜索与二叉排序树的时间性能（ C  ）。

 A．相同               B．完全不同    

C．有时不相同            D．数量级都是O(log_2n)

#对于二叉搜索树（BST），如果树是平衡的，那么在平均情况下，查找、插入和删除的时间复杂度也是 𝑂(log⁡𝑛)。但是，如果树不平衡，例如退化为链表，那么时间复杂度可能会变为 𝑂(𝑛)。

（7）分别以下列序列构造二叉排序树，与用其它三个序列所构造的结果不同的是（ C ）。 

A．（100，80， 90， 60， 120，110，130） 

B．（100，120，110，130，80， 60， 90）

C．（100，60， 80， 90， 120，110，130）

D．(100，80， 60， 90， 120，130，110)

（8）在平衡二叉树中插入一个结点后造成了不平衡，设最低的不平衡结点为A，并已知A的左孩子的平衡因子为0右孩子的平衡因子为1，则应作（ C ）型调整以使其平衡。

A．LL      B．LR     C．RL      D．RR

（9）下列关于m阶B-树的说法错误的是（ D ）。  

A．根结点至多有m棵子树   

B．所有叶子都在同一层次上

C．非叶结点至少有m/2 (m为偶数)或m/2+1（m为奇数）棵子树  

D．根结点中的数据是有序的

（10）下面关于哈希查找的说法，正确的是（ C ）。    

A．哈希函数构造的越复杂越好，因为这样随机性好，冲突小   

B．除留余数法是所有哈希函数中最好的  

C．不存在特别好与坏的哈希函数，要视情况而定

D．哈希表的平均查找长度有时也和记录总数有关

（11）下面关于哈希查找的说法，不正确的是（ A ）。       

 A．采用链地址法处理冲突时，查找一个元素的时间是相同的

 B．采用链地址法处理冲突时，若插入规定总是在链首，则插入任一个元素的时间是相同的

 C．用链地址法处理冲突，不会引起二次聚集现象

 D．用链地址法处理冲突，适合表长不确定的情况

（12）设哈希表长为14，哈希函数是H(key)=key%11，表中已有数据的关键字为15，38，61，84共四个，现要将关键字为49的元素加到表中，用二次探测法解决冲突，则放入的位置是（ D ）。

   A．8       B．3       C．5       D．9 

（13）采用线性探测法处理冲突，可能要探测多个位置，在查找成功的情况下，所探测的这些位置上的关键字 (  A)。

A．不一定都是同义词          B．一定都是同义词    

C．一定都不是同义词         D．都相同

## 排序（15个题）

（1）从未排序序列中依次取出元素与已排序序列中的元素进行比较，将其放入已排序序列的正确位置上的方法，称为（ C ）。

A．归并排序    B．冒泡排序    C．插入排序     D．选择排序 

（2）从未排序序列中挑选元素，并将其依次放入已排序序列（初始时为空）的一端的方法，称为（ D  ）。

A．归并排序    B．冒泡排序    C．插入排序    D．选择排序 

（3）对n个不同的关键字由小到大进行冒泡排序，在下列（ B ）情况下比较的次数最多。

A．从小到大排列好的         B．从大到小排列好的  

 C．元素无序             D．元素基本有序

（4）对n个不同的排序码进行冒泡排序，在元素无序的情况下比较的次数最多为（ D  ）。

A．n+1      B．n        C．n-1        D．n(n-1)/2

（5）快速排序在下列（ C ）情况下最易发挥其长处。

A．被排序的数据中含有多个相同排序码  

B．被排序的数据已基本有序  

C．被排序的数据完全无序     

D．被排序的数据中的最大值和最小值相差悬殊

（6）对n个关键字作快速排序，在最坏情况下，算法的时间复杂度是（ B ）。

A．O(n)      B．O(n^2)       C．O(nlog_2n)     D．O(n^3) 

（7）一组记录的排序码为（46, 79，56，38，40，84），则利用快速排序的方法，以第一个记录为基准得到的一次划分结果为（ C ）。

A．38，40，46，56，79，84        B．40，38，46，79，56，84

C．40，38，46，56，79，84        D．40，38，46，84，56，79

（8）下列关键字序列中，（ D ）是堆。

A．16，72，31，23，94，53        B．94，23，31，72，16，53 

C．16，53，23，94，31，72        D．16，23，53，31，94，72

#要判断一个序列是否是堆，我们需要查看序列是否满足堆的性质。

在最大堆中，对于任意节点 𝑖，其父节点 parent(𝑖) 的值大于等于 𝑖 节点的值。在最小堆中，对于任意节点 𝑖，其父节点 parent(𝑖) 的值小于等于 𝑖 节点的值。

（9）堆是一种（ B ）排序。

A．插入     B．选择     C．交换    D．归并

#在堆排序中，我们不断地从堆顶取出元素，并且对剩余的元素进行调整，使得堆的性质得到维护。这种不断地选择最大（或最小）元素的过程称为选择排序。因此，堆排序是一种选择排序算法。

（10）堆的形状是一棵（ C ）。

A．二叉排序树      B．满二叉树     C．完全二叉树   D．平衡二叉树

（11）若一组记录的排序码为（46，79，56，38，40，84），则利用堆排序的方法建立的初始堆为（ B ）。

A．79，46，56，38，40，84        B．84，79，56，38，40，46      

C．84，79，56，46，40，38        D．84，56，79，40，46，38

（12）下述几种排序方法中，要求内存最大的是（ C  ）。

A．希尔排序     B．快速排序     C．归并排序    D．堆排序

（13）下述几种排序方法中，（ C ）是稳定的排序方法。

A．希尔排序     B．快速排序     C．归并排序    D．堆排序

（14）数据表中有10000个元素，如果仅要求求出其中最大的10个元素，则采用( D )算法最节省时间。

A．冒泡排序     B．快速排序     C．简单选择排序  D．堆排序

（15）下列排序算法中，（ A ）不能保证每趟排序至少能将一个元素放到其最终的位置上。

A．希尔排序     B．快速排序     C．冒泡排序    D．堆排序
