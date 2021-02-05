# 二叉搜索树

## 内容来源

- https://mp.weixin.qq.com/s/ioyqagZLYrvdlZyOMDjrPw
- https://mp.weixin.qq.com/s/SuGAvV9zOi4viaeyjWhzDw

## 二叉搜索树

二叉搜索树（binary search tree，BST），定义如下：

- 对于任意一个节点，左子树的所有节点值小于该节点，右子树所有节点值大于该节点
- 以任意一个节点为根的子树都是二叉搜索树

主要特性：

- 前序遍历的结果为升序序列

## 题目

#### [二叉搜索树中第K小的元素](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def kthSmallest(self, root: TreeNode, k: int) -> int:
        cnt = 0
        res = 0
        def traverse(root, k):
            nonlocal cnt
            nonlocal res
            if not root:
                return 
            traverse(root.left, k)
            cnt += 1
            if cnt == k:
                res = root.val
                return 
            traverse(root.right, k)
        traverse(root, k)
        return res
```



错误：

```python
def kthSmallest(self, root: TreeNode, k: int) -> int:
        cnt = 0
        def traverse(root, k):
            nonlocal cnt
            if not root:
                return 
            traverse(root.left, k)
            cnt += 1
            if cnt == k:
                return root.val
            traverse(root.right, k)
        return traverse(root, k)
```

以题目示例的第一个来说明代码的错误：

- root.val=3，不为空，递归访问以1为根节点的左子树

  - 根节点不为空，继续递归访问左子树
    - 左子树根节点为空，返回None

  - cnt+=1，此时cnt=1，通过if语句返回1	`以root.val=3为根节点的左子树访问完毕`

- cnt+=1此时cnt=2，跳过if语句递归访问右子树
  - 按照上诉方法访问右子树返回None

最终函数的返回为None

修正：

错误的原因在于对于递归返回的结果不作处理，导致函数一直递归调用直到遍历整棵树返回None，或者root恰好满足cnt==k从而返回root.val。

```python
def kthSmallest(self, root: TreeNode, k: int) -> int:
        cnt = 0
        def traverse(root, k):
            nonlocal cnt
            if not root:
                return 
            left = traverse(root.left, k)
            if not left:
                return left
            cnt += 1
            if cnt == k:
                return root.val
            traverse(root.right, k)
            if not right:
                return iright
        return traverse(root, k)
```

上述方法改进：

上面的代码逻辑是根据BST的中序遍历性质设计的，无论k等于几，是很重要遍历整棵树。如果利用二叉搜索树的性质——左子树所有节点都比根节点小，右子树所有节点都比根节点大，则可以优化算法的时间复杂度。根节点的大小排名就是左子树节点个数加1，利用k与根节点的排名大小递归搜索子树，每棵子树按照上面的逻辑可以判断子树根节点的排名以及与要搜索的节点的大小关系，从而最终可以找到符合条件的解。这种改进，只需要维护每个节点的排名值或者以该节点为根节点的左子树的节点个数即可。

#### [把二叉搜索树转换为累加树](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def convertBST(self, root: TreeNode) -> TreeNode:
        sumn = 0
        def traverse(root):
            nonlocal sumn
            if not root:
                return
            traverse(root.right)
            sumn += root.val
            root.val =sumn
            traverse(root.left)
        traverse(root)
        return root
```

> 函数如果有返回，不同情况下的返回应当是同样含义；要么既没有返回，借助外部变量实现节点值得修改

#### [验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def isValidBST(self, root: TreeNode) -> bool:
        # 按照BST定义，根节点值大于左子树最大值，小于右子树最小值
        # 构造辅助函数，引进左子树最大值和右子树最小值
        def isValid(root, leftmax, rightmin):
            if not root:
                return True
            if leftmax != None and leftmax >= root.val:
                return False
            if rightmin != None and rightmin <= root.val:
                return False
            return isValid(root.left, leftmax, root.val) and isValid(root.right, root.val, rightmin)
        return isValid(root, None, None)
```

注：

- 如果题设的函数无法满足递归使用，可以使用辅助函数，注意到辅助函数的一个优点是可以按照我们的想法设定所需要的参数。二叉树中，如果当前节点会对下面的子节点有整体影响，可以通过辅助函数增长参数列表，借助参数传递信息。

- 注意下面的细节：

  ```python
  def p(x):
      if 0: print('correct')
  # p(1)不打印，因为默认0为FALSE，非零值为TRUE
  #判断不为None，就直接使用if x!=None，不要使用if x
  ```

#### 在BST中插入一个节点

插入的想法就是按照二叉搜索树的规则找到待插入的位置，将其做为叶子节点进行插入

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def insert(self, root: TreeNode, val) -> bool:
	if not root:
        	return TreeNode(val)
        if root.val < val:
            self.insert(root.right, val)
        if root.val > val:
            self.insert(root.left, val)
```

> BST的遍历操作主要利用其左小右大的性质进行优化，其他和普通的二叉树的递归模式一样

#### 在BST中删除节点

删除节点的难点在于——如果删除的不是叶子节点，需要剩下的节点调整为二叉搜索树。

存在的情况有下面三种：

- 删除的节点为叶子节点——直接删除
- 删除的节点只有一个孩子节点——只用其孩子节点替换位置
- 删除的节点有两个孩子节点——使用左子树最大的节点替换当前节点或者使用右子树最小节点替换当前节点。

```python
// 下面的代码情况3使用右子树最小的节点替换待删除的节点
//这里是替换值，然后删除右子树最小的节点，一般直接交换两个节点的指针而不是修改val
//函数返回的是节点
TreeNode deleteNode(TreeNode root, int key) {
    if (root == null) return null;
    if (root.val == key) {
        // 这两个 if 把情况 1 和 2 都正确处理了
        if (root.left == null) return root.right;
        if (root.right == null) return root.left;
        // 处理情况 3
        TreeNode minNode = getMin(root.right);
        root.val = minNode.val;
        root.right = deleteNode(root.right, minNode.val);
    } else if (root.val > key) {
        root.left = deleteNode(root.left, key);
    } else if (root.val < key) {
        root.right = deleteNode(root.right, key);
    }
    return root;
}

TreeNode getMin(TreeNode node) {
    // BST 最左边的就是最小的
    while (node.left != null) node = node.left;
    return node;
}
```

