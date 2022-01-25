# 剑指 Offer 28. 对称的二叉树

## 题目描述

这是LeetCode上的[对称的二叉树](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)，难度为简单

请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

<img src="images\image-20220124201146941.png" alt="image-20220124201146941" style="zoom:75%;" />

但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:

<img src="images\image-20220124201223518.png" alt="image-20220124201223518" style="zoom:75%;" />

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

$0\leq$节点个数 $\leq1000$​

***

## 解答

### recursion

首先进行特殊情况判定，如果root为空，则直接返回true，反之则调用递归helper函数。在helper函数中，我们验证当前节点的左右两节点的对称性。如果左右两节点皆为空，则对称，返回true；若左右两节点中有且仅有一个为空或均不为空但是值不相等，则返回false，若左右两节点值相等，则向下递归验证下一层是否对称，即：左节点的左节点和右节点的右节点是否对称，左节点的右节点和右节点的左节点是否对称。

#### Java实现

```java
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if(root == null){
            return true;
        }else {
            return isSymmetricHelper(root.left, root.right);
        }
    }
    public boolean isSymmetricHelper(TreeNode left, TreeNode right){
        if(left == null && right == null){
            return true;
        } else if(left == null || right == null || left.val != right.val){
            return false;
        } else return isSymmetricHelper(left.right, right.left) && isSymmetricHelper(left.left, right.right);
    }
}
```

#### C++实现

```c++
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(root == nullptr){
            return true;
        } else {
            return isSymmetricHelper(root->left, root->right);
        }
    }
private :
    bool isSymmetricHelper(TreeNode* left, TreeNode* right) {
        if(left == nullptr && right == nullptr){
            return true;
        } else if(left == nullptr || right == nullptr || left->val != right->val){
            return false;
        } else{
            return isSymmetricHelper(left->left, right->right) && isSymmetricHelper(left->right, right->left);
        }
    }
};
```

* 时间复杂度：因为最多调用$\frac{N}{2}$次递归，因此时间复杂度为$O(N)$
* 空间复杂度：因为什么都没新建，因此空间复杂度为$O(1)$