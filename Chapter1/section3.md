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

#### [最大二叉树](https://leetcode-cn.com/problems/maximum-binary-tree/)

分析：

- 找到最大值对应的位置，将数组切分，并且使用最大值构造根节点
- 对于切分成的左右子数组，递归构造左右子树

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def constructMaximumBinaryTree(self, nums: List[int]) -> TreeNode:
        # 寻找子数组中的最大值和索引，子数组通过首末元素索引定位
        def findmax(nums, start, end):
            maxval = nums[start]
            maxindex = start
            for i in range(start, end):
                if nums[i] > maxval:
                    maxval = nums[i]
                    maxindex = i
            root = TreeNode(maxval)
            root.left = self.constructMaximumBinaryTree(nums[:maxindex])
            root.right = self.constructMaximumBinaryTree(nums[maxindex+1:])
            return root

        if not nums:
            return 
        return findmax(nums, 0, len(nums))
```

> 只讲寻找最大值封装，其他部分在函数外为什么一直报递溢出？？？



#### [从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

分析：

- 由前序遍历中序遍历的规则可以找到根节点的位置，进而拆分序列为子树队一行的序列
- 难点在于前序序列如何正确划分出左右子树对应的序列。在中序序列中找到根节点就可划分左右子树对应的序列，从而可以求得左右子树的长度，就可在前序序列中找到左右子树对应的子序列。

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> TreeNode:
        def build(preorder, preStart, preEnd, inorder, inStart, inEnd):
            if preStart > preEnd:
                return 
            #  root 节点对应的值就是前序遍历数组的第一个元素
            rootVal = preorder[preStart]
            #  rootVal 在中序遍历数组中的索引
            index = inStart
            while index <= inEnd:
                if inorder[index] == rootVal:
                    break
                index += 1
            leftSize = index - inStart

            #  先构造出当前根节点
            root = TreeNode(rootVal)
            #  递归构造左右子树
            root.left = build(preorder, preStart + 1, preStart + leftSize, inorder, inStart, index - 1)
            root.right = build(preorder, preStart + leftSize + 1, preEnd, inorder, index + 1, inEnd)
            return root
        return build(preorder, 0, len(preorder) - 1, inorder, 0, len(inorder) - 1)
```

注意：

- 设置多个有关索引的问题时，为了防止混乱定义要统一，要么都是按照数组索引（从0开始）来写，要么都按照次序编写（从1开始）；要么都包含最后一个元素，要么都不包含。也就就是要明确函数的定义具体是什么（此处对应入参的含义）
- 把握递归的主题逻辑，但是不要忘了basecase

#### [从中序与后序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

分析：

- 想法和使用前序+中序类似，利用后序遍历序列的特点寻找根节点，在到中序序列中划分子树序列，使用子序列长度进一步划分后序序列

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def buildTree(self, inorder: List[int], postorder: List[int]) -> TreeNode:
        def build(inorder, startin, endin, postorder, startpost, endpost):
            # 入参是要考虑数组的完整始末索引
            # basecase
            if startpost > endpost:
                return 

            root = TreeNode(postorder[endpost])
            index = startin
            while index <= endin:
                if inorder[index] == postorder[endpost]:
                    break
                index += 1
            leftsize = index - startin
            root.left = build(inorder, startin, index-1, postorder, startpost, startpost+leftsize-1)
            root.right = build(inorder, index+1, endin, postorder, startpost+leftsize, endpost-1)
            return root
        
        return build(inorder, 0, len(inorder)-1, postorder, 0, len(postorder)-1)
```





#### [寻找重复的子树](https://leetcode-cn.com/problems/find-duplicate-subtrees/)

