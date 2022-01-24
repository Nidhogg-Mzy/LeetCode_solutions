# 剑指 Offer 54. 二叉搜索树的第k大节点

## 题目描述

这是LeetCode上的[二叉搜索树的第k大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/), 难度为简单

给定一棵二叉搜索树，请找出其中第 `k` 大的节点的值。

## 实例1

```
输入: root = [3,1,4,null,2], k = 1
   3
  / \
 1   4
  \
   2
输出: 4
```

### 实例2

```
输入: root = [5,3,6,2,4,null,null,1], k = 3
       5
      / \
     3   6
    / \
   2   4
  /
 1
输出: 4
```

### 限制

- $1 \leq k \leq $二叉搜索树元素个数

## 解答

由于给定的是一颗二叉搜索树，我们按inorder遍历这棵树得到的顺序即为递增顺序，因此第k大的节点即为倒数第k个节点

#### Java实现

```Java
class Solution {
    public int kthLargest(TreeNode root, int k) {
        ArrayList<Integer> arr = new ArrayList();
        inOrder(arr, root);
        return arr.get(arr.size() - k);
    }
    public void inOrder(ArrayList arr, TreeNode root){
        if(root != null){
            inOrder(arr, root.left);
            arr.add(root.val);
            inOrder(arr, root.right);
        }
    }
}
```

#### C++实现

```c++
class Solution {
public:
    int kthLargest(TreeNode* root, int k) {
        vector<TreeNode*> temp;
        inOrder(root, temp);
        return temp[temp.size() - k]->val;
    }
private:
    void inOrder(TreeNode* root, vector<TreeNode*>& temp) {
        if(root != nullptr){
            inOrder(root->left, temp);
            temp.emplace_back(root);
            inOrder(root->right, temp);
        }
    }
};
```

* 时间复杂度：因为我们遍历了一遍整个二叉搜索树，因此时间复杂度为$O(n)$
* 空间复杂度：因为我们新建了一个`ArrayList`来存中序遍历的结果，因此空间复杂度为$O(n)$
