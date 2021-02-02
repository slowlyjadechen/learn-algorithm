# 递归——二叉树篇

## 总结

<span style="color:red">把题目的要求细化，搞清楚根节点应该做什么，然后剩下的事情抛给前/中/后序的遍历框架就行了</span>

- <u>写递归算法的关键是要明确函数的「定义」是什么，然后相信这个定义，利用这个定义推导最终结果，绝不要跳入递归的细节。</u>

- <u>写树相关的算法，都是基于递归框架的</u>
  - <u>先搞清楚当前 root 节点该做什么，然后根据题目要求选择使用前序/中序/后续的递归框架，</u>
  - <u>根据函数定义递归调用子节点，递归调用会让孩子节点做相同的事情。</u>

- <u>二叉树题目的难点在于如何通过题目的要求思考出每一个节点需要做什么</u>

#### [翻转二叉树](https://leetcode-cn.com/problems/invert-binary-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def invertTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return root
        left = self.invertTree(root.right)
        right = self.invertTree(root.left)
        root.left = left
        root.right = right
        return root
```

#### [填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

目标：将二叉树每一层的节点按照从左到右相邻的关系，使用next指针连接起来，最后一个节点指向空。

分析：

问题的难点在于，**不同父节点的相邻两个节点如何关联next指针**。使用一般的二叉树的遍历框架，只能关联同一个父节点的两个孩子节点，但是不同父节点的同一层的节点无法关联。

目前的想法有两种：

- 使用层次遍历，并且标记（或使用其他方法）区分不同层节点，从而将每一层元素对于next指针正确设定；
- 使用辅助函数。

如果使用辅助函数，从主要问题出发考虑函数功能的设置问题。**单独使用一个节点如法解决问题，因此辅助函数接受两个节点作为参数——对应两个相邻需要设置next指针的节点。**

函数的主要功能就是按照题设的规则**正确设置输入的两个节点及其子节点的所有next指针**，需要完成以下事情：

- 正确设置这两个节点之间的next指针

- 正确设置两个节点的子节点的next指针。由于他们是相邻的，因此子节点的next指针设置包括相同父节点和不同父节点两种情况。（递归解决）
- 如果其中一个节点为空，则返回（basecase）

```python
"""
# Definition for a Node.
class Node:
    def __init__(self, val: int = 0, left: 'Node' = None, right: 'Node' = None, next: 'Node' = None):
        self.val = val
        self.left = left
        self.right = right
        self.next = next
"""

class Solution:
    def connect(self, root: 'Node') -> 'Node':
        def linkadjacentNode(node1, node2):
            if not node1 or not node2:
                return
            node1.next = node2
            linkadjacentNode(node1.left, node1.right)
            linkadjacentNode(node2.left, node2.right)
            linkadjacentNode(node1.right, node2.left)
            return 

        if not root:
            return root
        linkadjacentNode(root.left, root.right)
        return root
```



#### [二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def flatten(self, root: TreeNode) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        if not root:
            return root
        leftnew = self.flatten(root.left)
        p = root.left
        pre = root
        while p:
            pre = p
            p = p.right
        pre.right = self.flatten(root.right)
        # 如果只有右孩子，注意这里是否有问题
        if root.left:
            root.right = leftnew 
        root.left = None
        return root
```

上面的方法最后加了一个判断的由来是在拼接已经转化好的子节点的时候，拼接次序导致的。下面的代码参考lubuladong博客中的连接次序，更加简洁：

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def flatten(self, root: TreeNode) -> None:
        """
        Do not return anything, modify root in-place instead.
        """
        if not root:
            return 
        self.flatten(root.left)
        self.flatten(root.right)
        leftnew =  root.left
        rightnew = root.right
        root.left = None
        root.right = leftnew
        
        p = root
        while p.right:
            p = p.right
        p.right = rightnew
```



