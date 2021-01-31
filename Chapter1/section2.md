# section2 手把手刷链表题目（递归思维）

内容为lubuladong算法小抄的个人总结，[来源](https://labuladong.gitbook.io/algo/shu-ju-jie-gou-xi-lie/shou-ba-shou-shua-lian-biao-ti-mu-xun-lian-di-gui-si-wei)

## 递归反转链表的一部分

[LeetCode 92 反转链表II

[](https://leetcode-cn.com/problems/reverse-linked-list-ii/)

题目描述：

![](https://github.com/slowlyjadechen/learn-algorithm/tree/main/Chapter1/picture/reverselist.png)

按照如下思路对于问题进行探讨：

- 反转整个链表
- 反转链表的前n个
- 反转链表的m到n

```python
# 单链表的基本结构
class ListNode:
    def __init__(x):
        self.val = x
        self.next = None
```

### 反转整个链表

- 如果没有节点或者只有一个节点，直接返回head即可

- 如果存在多个节点

  <img src="https://github.com/slowlyjadechen/learn-algorithm/tree/main/Chapter1/picture/traverseall.png" alt="traverseall" style="zoom:80%;" />

  - 先反转除了头结点以外的所有节点，注意到，<u>反转的函数输入为待反转的链表的头结点，返回的是反转后的链表的头结点——即原始待反转链表的最后一个节点</u>，记为last。
  - 这时，问题就简化为反转只有两个节点的链表（将头结点的后继节点反转的结果视为一整个节点），只需改动节点后继指针即可实现。

  <img src="https://github.com/slowlyjadechen/learn-algorithm/tree/main/Chapter1/picture/traverseall2.png" alt="traverseall2" style="zoom:80%;" />

  - 最后输出last作为整个函数的结果。

```python
def traverse(head):
    #没有节点或者只有一个节点，直接返回head
    if not head or not head.next:
        return head
    
    #存在多个节点,先反转除了头结点以外的所有节点
    last = traverse(head.next)
    #修改后继指针的指向
    head.next.next = head
    head.next = None
    return last
```



### 反转前n个节点

可以按照反转整个链表的想法来考虑，会发现如果整个链表的长度大于n，存在下面这样一个问题：

**最终结果中，head的next应该指向第n+1个节点，而不是像反转整个链表中那样直接令其为None，如何找到第n+1个节点并将它记录下来？**

解决方法是使用一个额外的指针指向第n+1个节点。注意到，递归的base case为一个反转一个节点，递归的时候是将问题一步步缩小的，达到第n个节点的时候恰好为basecase，因此当反转的长度等于1时，记录下来的下一个节点就是需要的n+1个节点。

```python
successor  = None
def traverse(head, n):
    #如果n不等于1，当递归访问到最后一个节点时，还会运行下面的代码，修改successor记录下第n+1个节点，递归开始向上返回结果
    if n == 1:
        successor = head.next
        return head
    last = traverse(head.next, n-1)
    head.next.next = head
    head.next = successor
    return last
```



### 反转m到n节点

- 当m=1，问题就变成反转前n个节点
- 当m>1，如果从第二个节点开始，问题规模变成变成反转m-1到n-1个节点，按照常规递归的方法处理就行。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseBetween(self, head: ListNode, m: int, n: int) -> ListNode:
        successor = None
        def traverseN(node, n):
            nonlocal successor
            if n == 1:
                successor = node.next
                return node
            last = traverseN(node.next, n-1)
            node.next.next = node
            node.next = successor            
            return last

        if m == 1:
            return traverseN(head, n)
        head.next = self.reverseBetween(head.next, m-1, n-1)
        return head
```

### 总结

递归要注意以下几个点：

- 要有basecase
- 递推的模式
- **注意函数的输入输出的含义，不要跳进递归函数里面，而是从整体的逻辑上进行把握**
- 递归操作链表并不高效。和迭代解法相比，虽然时间复杂度都是 O(N)，但是迭代解法的空间复杂度是 O(1)，而**递归解法需要堆栈**，空间复杂度是 O(N)。

## 如何k个一组反转链表

LeetCode地址：https://leetcode-cn.com/problems/reverse-nodes-in-k-group/

题目描述：

<img src="https://github.com/slowlyjadechen/learn-algorithm/tree/main/Chapter1/picture/kgrouptraverse.png" alt="kgrouptraverse" style="zoom:80%;" />

分析：

- basecase：链表长度不足k时不进行分反转。因此进行反转之前需要判断长度是否大于k
- 对于长度对于k，先反转前k个，然后将结果和反转后面所有节点的结果连接，就可以得到最后的结果。
  - 由于要反转前k个之前会判断长度，也就得到了前k个链表的完整情况，因此反转前k个相当于反转长度为k的整个链表。
  - 要反转后面的链表，需要知道后面链表的头结点，因此在判断长度时，如果长度满足，还要记下第k+1个元素

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def reverseKGroup(self, head: ListNode, k: int) -> ListNode:
        next1 = head
        for i in range(k):
            if not next1:
                return head
            next1 = next1.next            

        last, cur, next2 = self.reverseKGroup(next1, k), head, head
        for i in range(k):
            next2 = cur.next
            cur.next = last
            last = cur
            cur = next2
        return last
```



## 如何判断回文链表

https://leetcode-cn.com/problems/palindrome-linked-list/

题目描述：

<img src="https://github.com/slowlyjadechen/learn-algorithm/tree/main/Chapter1/picture/palindromelinklist.png" alt="palindromelinklist" style="zoom:80%;" />

分析：

如果是判断回文字符串，可以从两端向中间依次判断。对于此处，问题在于链表不能从后向前遍历。那么主要问题在于如何等价实现从后向前的遍历。可行的方法有如下三种：

1. 反转整个链表，然后逐个比较反转前后对于位置节点value是否相等(或者将链表的值逐个打印转化成列表或者字符串的形式，利用其它容易倒序访问的结构来判断）。

```python
#使用头插法创建新的链表，为反转原始链表的结果
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        pre, cur = None, head
        while cur:
            newnode = ListNode(cur.val)
            newnode.next = pre
            pre = newnode
            cur = cur.next
            
        while pre:
            if pre.val == head.val:
                pre = pre.next
                head = head.next
            else:
                return False
        return True

```

2. **单链表可以看作是只有左子树或者只有右子树的二叉树，使用二叉树的遍历方法来实现**（`原文这里写的太妙了！`）。如果将其看做只有右子树，那么后序遍历打印出的节点的值就是单链表倒序的节点的值，可以借助二叉树后序遍历的递归形式实现。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        def traverse(right):
            global left 
            left = head
            if not right:
                return True
            res = traverse(right.next)
            res = res and (right.val==left.val)
            left = left.next
            return res 

        return traverse(head)
```

3. 上面的方法空间复杂度都是O(n)。可以使用快慢指针找到链表的中间节点，只反转链表的后半部分就可实现后半部分的倒序访问，从而判断是否问回文。

```python
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def isPalindrome(self, head: ListNode) -> bool:
        if head:
            slow, fast = head, head
            while  fast and  fast.next:
                slow = slow.next
                fast = fast.next.next
            if fast:
                slow = slow.next

            pre, cur, next1 = None, slow, slow
            while cur:
                next1 = cur.next
                cur.next = pre
                pre = cur
                cur = next1
            while pre:
                if pre.val == head.val:
                    pre = pre.next
                    head = head.next
                else:
                    return False
        return True
```

总结：

- 寻找回文串是从中间向两端扩展，判断回文串是从两端向中间收缩。
- 对于单链表，无法直接倒序遍历，可以造一条新的反转链表，可以利用链表的后序遍历，也可以用栈结构倒序处理单链表。具体到回文链表的判断问题，由于回文的特殊性，可以不完全反转链表，而是仅仅反转部分链表，将空间复杂度降到 O(1)。