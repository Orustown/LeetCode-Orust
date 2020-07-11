**[206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)**

### 迭代思路详细分析
转化过程:
链表`1 → 2 → 3 → Ø`需要转化为`Ø ← 1 ← 2 ← 3`

需要三轮循环:
`Ø ← 1`:prev     curr:`1 → 2 → 3 → Ø`

`Ø ← 1 ← 2`:prev     curr:`2 → 3 → Ø`

`Ø ← 1 ← 2 ← 3`:prev     curr:`3 → Ø`

以第二轮循环为例，转化过程为:

1. nexttemp作为一个中间变量，暂时存储了curr后面的元素`nexttemp = curr.next`，即此时nexttemp为`2 → 3 → Ø`，并在一轮循环的**最后**还给curr，curr是**转化前的链表的头**

2. prev是**转化后的链表的头**，如上转化过程所示，执行`curr.next = prev`，因为curr.next已经被存储了，所以现在curr要指向另一边，实现反转，反转后此时`Ø ← 1 ← 2`:curr

3. 方向已经反转过了，就将curr赋值给prev，执行`prev = curr`，此时`Ø ← 1 ← 2`:prev，**因为curr还需要下一轮继续使用**

4. 因为curr还需要下一轮继续使用，所以执行`curr = nexttemp`让**curr重新变为**:`2 → 3 → Ø`，此时`Ø ← 1 ← 2`:prev   curr:`2 → 3 → Ø`，进入下一轮循环

最后返回prev即可


### 代码

```python3
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        prev = None
        curr = head
        while(curr != None):
            nexttemp = curr.next
            curr.next = prev
            prev = curr
            curr = nexttemp
        return prev
            
```

### 递归
可以画图去理解每一步

### 递归代码1：从后往前直接递归，画图理解，学习
```python3
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        if head is None or head.next is None:
            return head
        newhead = self.reverseList(head.next)
        head.next.next = head
        head.next = None
        return newhead
```


### 递归代码2：另外定义一个递归函数，从前往后递归，和迭代方向相同，不同的递归方式，也好理解

```python3
class Solution:
    def reverseList(self, head: ListNode) -> ListNode:
        def help(pre, cur)
            if cur == None:
                return pre
            else:
                nextNode = cur.next
                cur.next = pre
                return reverse(cur, nextNode)
        return help(None, head)
```


------------------------------
#  ---------------------- 我是一道分割线----------------------------

------------------------------

**[92. 反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)**


### 解题思路
先建一个dummy节点，指向链表的头结点，这样的话就算头结点变动了，还可以通过`dummy.next`来获得新链表的头结点。

本题的例子：
初始时，`dummy -> 1 -> 2 -> 3 -> 4 -> 5 -> NULL`，pre指针从dummy开始向后遍历。

当pre指向结点1，cur指向结点2的时候，建立一个临时的结点tmp，
`tmp = cur.next`：tmp第一次先指向节点3（为了断开2与3之间的连接），
`cur.next = tmp.next`：然后断开2和3，将2的next连到4
`tmp.next = pre.next`：再把3连到1的后面的结点（即2）的前面
`pre.next = tmp`：最后再将原来的1和2的连接断开，将1连到3

![image.png](https://pic.leetcode-cn.com/f384fe30667e6ce6251b88f5762aba69d096eff72af62db8297f47ff40f01b01-image.png)


### 代码

```python3
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def reverseBetween(self, head: ListNode, m: int, n: int) -> ListNode:
        dummy = ListNode(None)
        dummy.next = head
        pre = dummy
        
        i = 0
        while i < m - 1:
            i += 1
            pre = pre.next
        cur = pre.next
        i += 1  # i = m
        while i < n:
            i += 1
            tmp = cur.next
            cur.next = tmp.next
            tmp.next = pre.next
            pre.next = tmp
        return dummy.next
     
```


------------------------------
#  ---------------------- 我是一道分割线----------------------------

------------------------------

**[25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)**


### 解题思路
将206和92的代码稍作修改，即可做出25题，它的变型题也是大同小异，
**熟练掌握链表的操作，夯实基础！**

[206. 反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)
[92. 反转链表 II](https://leetcode-cn.com/problems/reverse-linked-list-ii/)
[25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)，最后剩余的节点保持原有顺序
[变型题]25.K 个一组翻转链表，最前面剩余的节点保持原有顺序


每次反转一段长度的节点
外循环中，pre每次指向的是所要反转的链表的前一个节点，cur每次指向的是所要反转的最后一个节点，
每次将cur.next传入循环，nxt节点，即所要反转的链表的后一个节点，用来判断是否反转完。

在函数内（内循环）的反转基本上与92一样。

### 代码

```python3
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None


# 原题目：
# 头条、阿里面试题

class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        dummy = ListNode(None)
        dummy.next = head
        pre = dummy
        cur = pre.next
        
        i = 0
        while cur:
            i += 1
            if i % k == 0:
                pre = self.reverseList(pre, cur.next)
                cur = pre.next
            else:
                cur = cur.next
                
        return dummy.next

    def reverseList(self, pre: ListNode, nxt: ListNode) -> ListNode:
        tmp = pre.next
        cur = tmp.next
        while(cur != nxt):
            tmp.next = cur.next
            cur.next = pre.next
            pre.next = cur
            cur = tmp.next
        return tmp
        



# [变型题]25.K 个一组翻转链表，最前面剩余的节点保持原有顺序
# 头条、阿里面试题

# 如果将原题中的：“如果节点总数不是 k 的整数倍，那么请将最后剩余的节点保持原有顺序“
# 改为：“如果节点总数不是 k 的整数倍，那么请将最前面剩余的节点保持原有顺序”
# 则可以将代码稍作修改，如下：
class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        dummy = ListNode(None)
        dummy.next = head
        pre = dummy
        
        # 先计算链表长度
        n = 0
        cur = head
        while cur:
            cur = cur.next
            n += 1
        # 将pre指针和i定位到需要反转的链表的前一个位置
        i = 0
        while (n - i) % k != 0:
            pre = pre.next
            i += 1
        # cur依然是需要反转的链表的第一个节点
        cur = pre.next
        
        while cur:
            i += 1
            if (n - i) % k == 0:  # 更改需要反转的条件
                pre = self.reverseList(pre, cur.next)
                cur = pre.next
            else:
                cur = cur.next
                
        return dummy.next

    def reverseList(self, pre: ListNode, nxt: ListNode) -> ListNode:
        tmp = pre.next
        cur = tmp.next
        while(cur != nxt):
            tmp.next = cur.next
            cur.next = pre.next
            pre.next = cur
            cur = tmp.next
        return tmp
```
