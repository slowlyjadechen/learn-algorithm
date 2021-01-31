# section1

[来源](https://labuladong.gitbook.io/algo/shu-ju-jie-gou-xi-lie)

## 整体学习思路

### 学习指南

#### 数据的存储结构只有两种：

- 数组（顺序存储）
  - 优点：
    - 紧凑的连续存储，节省空间
    - 可以随机访问，查询方便
  - 缺点：
    - 扩容麻烦。需要将所有元素复制到新的位置
    - 插入删除麻烦，需要移动后面的所有元素
- 链表（链式存储）
  - 优点：
    - 如果知道元素的前驱或者后继，插入删除容易
  - 确定：
    - 不能随机访问
    - 需要分配额外的空间存储指针

#### 数据结构的基本操作

`遍历`+`操作(访问增删改查)`

遍历常用的形式主要是下面两种：

- 线性——for/while为代表

```python
def traverse(arr):
    for i in range(len(arr)):
    	# 迭代访问 arr[i]
```

- 非线性——递归为代表

```python
# 定义一个链表
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
#线性访问        
def traverse(head):
    p = head
    for not p:
        #访问p.val
        p = p.next
#递归访问
def traverse(head):
   #访问head.val
	traverse(head.next)
```

```python
#二叉树遍历
def TreeNode:
    def __init__(x):
        self.val = x
        self.left = None
        self.right = None

def traverse(root):
    traverse(root.left)
    traverse(root.right)
```



### 推荐书目

[算法4](https://item.jd.com/11098789.html)

不用看数学证明和课后题

### 学习之路

数据结构就是一以数组、链表为基础的一些特定操作。每个数据结构的基本操作只有增删改查。

刷题的要点在于找到问题背后的解法框架。

最好从二叉树开始。

