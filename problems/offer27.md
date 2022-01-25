# 剑指 Offer 27. 二叉树的镜像

## 题目描述

这是LeetCode上的[二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/), 难度为简单。

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

例如输入:

<img src="images\image-20220114155804968.png" alt="image-20220114155804968" style="zoom:65%;" />

镜像输出:

<img src="images\image-20220114155839030.png" alt="image-20220114155839030" style="zoom:65%;" />

### 示例1

```
输入：root = [1,2,2,3,4,4,3]
输出：true
```

### 示例2

```
输入：root = [1,2,2,null,3,null,3]
输出：false
```



### 限制

$0\leq$ 节点个数 $ \leq1000$

***

## 解答

### 1. recursion

如果`root`是`null`的话，就直接`return null`.

如果`root`不是`null`, 则交换`root.right` 和`root.left`，然后分别递归镜像`root.right`和`root.left`, 最后返回`root`.

#### Java 实现

```Java
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if(root == null){
            return null;
        }
        TreeNode temp = root.right;		//交换root.right和root.left
        root.right = root.left;
        root.left = temp;
        mirrorTree(root.left);		//镜像root.left
        mirrorTree(root.right);		//镜像root.right
        return root;
    }
}
```

#### C++实现

```C++
class Solution {
public:
    TreeNode* mirrorTree(TreeNode* root) {
        if(root == nullptr){		//特殊情况判断如果root是nullptr直接返回nullptr
            return nullptr;
        }
        root->right = mirrorTree(root->right);
        root->left = mirrorTree(root->left);
        TreeNode* temp = root->right;
        root->right = root->left; 
        root->left = temp;
        return root;
    }
};
```

#### Go实现

``` Go
func mirrorTree(root *TreeNode) *TreeNode {
    if root == nil {
        return nil
    }
    root.Left, root.Right = mirrorTree(root.Right), mirrorTree(root.Left)
    return root
}
```

* 时间复杂度：由于我们遍历二叉树的所有节点并在常数时间内对其进行交换，因此时间复杂度为$O(n)$
* 空间复杂度：由于我们对于每个节点递归一遍，每次递归的时候新建一个`TreeNode`，因此空间复杂度为$O(n)$

