# 二叉树的序列化和反序列化

## 来源

https://mp.weixin.qq.com/s/DVX2A1ha4xSecEXLxW_UsA

## 序列化和反序列化

JSON 的运用非常广泛，比如我们经常将变成语言中的结构体序列化成 JSON 字符串，存入缓存或者通过网络发送给远端服务，消费者接受 JSON 字符串然后进行反序列化，就可以得到原始数据了。这就是「序列化」和「反序列化」的目的，以某种固定格式组织字符串，使得数据可以独立于编程语言。

## 例——[二叉树的序列化与反序列化](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)

二叉树的遍历方式：

- 递归方式：前中后遍历
- 迭代方式：层次遍历

**单纯由一种遍历序列无法唯一确定一颗二叉树的原因是缺少空指针的信息，但是当把空指针的信息也包含时，除了中序序列以外其他三种序列都可以还原出真实的二叉树**。

### 前序遍历法

注意两点：

- 代码中添加个分隔符`逗号`的位置，应该加在真正返回值的后面，返回值的两种情况：节点为空和返回根节点，这在后序遍历中同样要注意
- 反序列化函数中存在`nodes.pop()`，对于前序遍历方法而言这一步不需要，不需要的原因是序列化按照完全二叉树的形式构造的（因为将null编码为#），因此最后只剩一个空字符串时，整个函数已经运行完成退出（每个节点只含有两个子树）。但是这个问题在后序遍历方法中需要注意，因为`nodes.split(',')`最后一个元素为空字符串，从后向前遍历必然要遇到

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Codec:
    def serialize(self, root):
        """Encodes a tree to a single string.
        :type root: TreeNode
        :rtype: str
        """
        if not root: return '#,'

        data = ''
        data += str(root.val) + ','
        data += self.serialize(root.left)
        data += self.serialize(root.right)
        return data
        
    def deserialize(self, data):
        """Decodes your encoded data to tree.
        :type data: str
        :rtype: TreeNode
        """
        nodes = data.split(',')
        nodes.pop()
        def treenodes(nodes):
            temp = nodes.pop(0)
            if temp == '#':
                root = None
                return root
            root = TreeNode(int(temp))
            root.left = treenodes(nodes)
            root.right = treenodes(nodes)
            return root
        return treenodes(nodes)

# Your Codec object will be instantiated and called as such:
# codec = Codec()
# codec.deserialize(codec.serialize(root))
```

### 后序遍历

后序遍历需要注意的问题是反序列化是从后向前遍历，因此子树构建的顺序是先构建右子树再构建左子树

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Codec:
# 使用后序遍历
    def serialize(self, root):
        """Encodes a tree to a single string.       
        :type root: TreeNode
        :rtype: str
        """
        if not root: return '#,'
        
        data = ''
        data += self.serialize(root.left)
        data += self.serialize(root.right) 
        data += str(root.val) + ','
        return data

    def deserialize(self, data):
        """Decodes your encoded data to tree.   
        :type data: str
        :rtype: TreeNode
        """
        nodes = data.split(',')
        nodes.pop()
        def treenodes(nodes):
            temp = nodes.pop()
            if temp == '#':
                root = None
                return root
            root = TreeNode(int(temp))
            root.right = treenodes(nodes)
            root.left = treenodes(nodes)
            return root
        return treenodes(nodes)

# Your Codec object will be instantiated and called as such:
# codec = Codec()
# codec.deserialize(codec.serialize(root))
```

### 层次遍历

```python
# Definition for a binary tree node.
# class TreeNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Codec:
# 使用层次遍历
    def serialize(self, root):
        """Encodes a tree to a single string.    
        :type root: TreeNode
        :rtype: str
        """
        if not root: return '#,'
        queue = [root]
        data = ''
        while queue:
            temp = queue.pop(0)
            if not temp:
                data += '#,'
                continue
            data += str(temp.val) + ','
            queue.append(temp.left)
            queue.append(temp.right)
        return data

    def deserialize(self, data):
        """Decodes your encoded data to tree.    
        :type data: str
        :rtype: TreeNode
        """
        nodes = data.split(',')
        nodes.pop()
        if len(nodes) == 1: return None
        root = TreeNode(nodes[0])
        parent = [root]
        for i in range(1, len(nodes), 2):
            t_node = parent.pop(0)
            if nodes[i] == '#':
                t_node.left = None
            else:
                t_left = TreeNode(int(nodes[i]))
                t_node.left = t_left
                parent.append(t_left)
            
            if nodes[i+1] == '#':
                t_node.right = None
            else:
                t_right = TreeNode(int(nodes[i+1]))
                t_node.right = t_right
                parent.append(t_right)
        return root
# Your Codec object will be instantiated and called as such:
# codec = Codec()
# codec.deserialize(codec.serialize(root))
```

> 层次遍历使用队列辅助进行，关键在于如何记录当前节点的父节点，队列中应该加入什么样的节点