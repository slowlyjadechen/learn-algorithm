# section6

## 来源

内容整理自lubuladong公众号文章，链接如下：

- https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247485629&idx=1&sn=fc0d0fc2b8618a9b8a575cfa9d5b1c4a&chksm=9bd7f6b5aca07fa33c4fbce0dc439359592ace091814fdcdc0742f336722398090396f0b3668&scene=21#wechat_redirect
- https://mp.weixin.qq.com/s?__biz=MzAxODQxMDM0Mw==&mid=2247485561&idx=1&sn=a394ba978283819da1eb34a256f6915b&chksm=9bd7f671aca07f6722f0bc1e946ca771a0a40fd8173cc1227a7e0eabfe4e2fcc57b9ba464547&scene=21#wechat_redirect
- 

#### [扁平化嵌套列表迭代器](https://leetcode-cn.com/problems/flatten-nested-list-iterator/)

`对于花里胡哨的题设，要从本质原理出发，归纳出其底层的结构类型，并由此出发探究解决方法`

通过题目的描述可知，NestedInteger应当具有如下特点：

- 元素可以是一个整数，或者一个NestedInteger
- NestedInteger具有isInteger，getInteger，getList三种API

由此推测NestedInteger的实现如下：

```python
class NestedInteger:
    def __init__(self, val, list):
        """
        node is one  of val or list, i.e. if val is an integer, then self.list=None, vice versa.
        """
        self.val = val
        self.list = list
   def isInteger(self) :
       """
       @return True if this NestedInteger holds a single integer, rather than a nested list.
       """
       return self.val != None

   def getInteger(self) -> int:
       """
       @return the single integer that this NestedInteger holds, if it holds a single integer
       Return None if this NestedInteger holds a nested list
       """
       return self.val

   def getList(self) -> [NestedInteger]:
       """
       @return the nested list that this NestedInteger holds, if it holds a nested list
       Return None if this NestedInteger holds a single integer
       """ 
       return self.list
```

可见，该结构和N叉树的结构是一样的：

```java
//java
class NestedInteger {
    Integer val;
    List<NestedInteger> list;
}

/* 基本的 N 叉树节点 */
class TreeNode {
    int val;
    TreeNode[] children;
}
```

- **叶子节点是`Integer`类型，其`val`字段非空；**

- **其他节点都是`List<NestedInteger>`类型，其`val`字段为空，但是`list`字段非空，装着孩子节点**。

比如说输入是`[[1,1],2,[1,1]]`，其实就是如下树状结构：

<img src="E:\document\笔记\数据结构和算法lubuladong\chapter1\Ntree.png" alt="Ntree" style="zoom:80%;" />

因此，将NestedInteger扁平化本质上就是遍历一颗N叉树，将所有叶子节点的值取出来。

如果将所有叶子节点实现取出来，然后在遍历，得到的代码如下：

```python
# """
# This is the interface that allows for creating nested lists.
# You should not implement it, or speculate about its implementation
# """
#class NestedInteger:
#    def isInteger(self) -> bool:
#        """
#        @return True if this NestedInteger holds a single integer, rather than a nested list.
#        """

#    def getInteger(self) -> int:
#        """
#        @return the single integer that this NestedInteger holds, if it holds a single integer
#        Return None if this NestedInteger holds a nested list
#        """

#    def getList(self) -> [NestedInteger]:
#        """
#        @return the nested list that this NestedInteger holds, if it holds a nested list
#        Return None if this NestedInteger holds a single integer
#        """

class NestedIterator:
    def __init__(self, nestedList: [NestedInteger]):
        
    
    def next(self) -> int:
        
    
    def hasNext(self) -> bool:
         

# Your NestedIterator object will be instantiated and called as such:
# i, v = NestedIterator(nestedList), []
# while i.hasNext(): v.append(i.next())
```

上面的解法是一次性将所有叶子节点的值取出来，然后 通过列表缓存到内存中，无论取几个值都会将所有节点计算一遍，当数据量很大的时候会很慢。迭代器应当是惰性的，每次只计算需要的结果，而不是一次计算所有的。而且为值的节点都是叶子节点，即我们关心的只有叶子节点，因此DFS和BFS都不行。

实际的思路如下：

- 调用`hasNext`时，如果`nestedList`的第一个元素是列表类型，则不断展开这个元素，直到第一个元素是整数类型。

- 由于调用`next`方法之前一定会调用`hasNext`方法，这就可以保证每次调用`next`方法的时候第一个元素是整数型，直接返回并删除第一个元素即可。

```python
class NestedIterator:
    def __init__(self, nestedList: [NestedInteger]):
        self.stack = nestedList[::-1]
    
    def next(self) -> int:
        return self.stack.pop().getInteger()
    
    def hasNext(self) -> bool:
         while self.stack and self.stack[-1].isInteger() == False:
                self.stack += self.stack.pop().getList()[::-1]
         return len(self.stack) > 0
```

#### [二叉树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

递归型的问题分析思路和动态规划类型的问题相似，无非就是灵魂三问：

- 定义——这个函数是干嘛的

- 状态——这个函数参数中的变量是什么

- 选择——得到函数的递归结果，你应该干什么

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
        """
        这里basecase实际上说明了函数的功能：寻找第一个为p或者q的节点
        整个框架是后序遍历，因此是从下往上找，通过返回值的特性判断出正确的结果
        因此返回结果就是题目要求的
        """”
        #basecase
        if not root: return root
        if root == p or root == q: return root
        
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root
        if not left and not right:
            return None
        return left if left else right
```

这里返回结果是最大公共祖先的原因：basecase只是特殊情况或者是直到叶子节点才会发生的情况，实际的运行过程时先判断左右子树，在判断根节点，因此算法的过程实际上是一个后序遍历（从下往上），因此得出的结果就是第一个公共祖先。

> 先序遍历是从上往下

#### 计算完全二叉树的节点数目

- 满二叉树：每个节点只有两个或者零个孩子节点

- 完全二叉树：每个节点按照从上到下、从左到右的次序依次编号，各个节点的序号和在相同深度的满二叉树中的序号相同。

  > <span style ="color:red">一棵完全二叉树的两棵子树，至少有一棵是满二叉树</span>

> 二叉树相关的问题，注意利用树的特性进行优化，比如这里完全二叉树子树的特性，BST的左右子树的大小性质

##### 计算二叉树的节点数目

```python
def nodescount(root):
    if not root:
        return 0
    else:
        return 1 + nodescount(root.left) + nodescount(root.right)
```

##### 计算满二叉树节点数目

```python
def nodescount(root):
    deep = 0
    p = root
    while p:
        deep += 1
        p = p.left
    return math.pow(2, deep) -1
```

##### 计算完全二叉树的节点数目

```python
def nodescount(root):
    left, right = root, root
    deep1, deep2 = 0, 0
    while left:
        deep1 += 1
        left = left.left
    while right:
        deep2 += 1
        right = right.right
    if deep1 == deep2: 
        return math.pow(2, deep1) - 1
    return 1 + nodescount(root.left) + nodescount(root.right)
```



