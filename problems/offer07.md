# 剑指offer07. 重建二叉树

## 题目描述

这是LeetCode上的[重建二叉树](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)，难度为简单

输入某二叉树的前序遍历和中序遍历的结果，请构建该二叉树并返回其根节点。

假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

### 示例1

<img src="images\image-20220126190852749.png" alt="image-20220126190852749" style="zoom:50%;" />

```
Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]
```

### 示例2

```
Input: preorder = [-1], inorder = [-1]
Output: [-1]
```

### 限制

$0 \leq$ 节点个数 $ \leq 5000$

***

## 解答

### 1.递归

首先我们需要知道中序遍历和前序遍历的特性。

中序遍历：先遍历左子树，再遍历根节点，再遍历右子树。

前序遍历：先遍历根节点，再遍历左子树，再遍历右子树。

新建HashMap来方便寻找递归中根节点的位置，遍历一遍给定中序遍历来构建我们的`HashMap`。在递归函数中，首先进行特殊情况判断，如果将要进行递归的区间左端点位于区间右端点右侧，则返回空值。由于前序遍历的特性，前序遍历序列的第一个元素一定是当前子树中的根节点，因此我们就可以获得这个根节点在inorder中的位置，并根据根节点的值新建一个用来返回的根节点。由于已知中序遍历，根据中序遍历特性，我们可以知道在根节点左边的全部是左子树的组成元素，在根节点右边的全部是右子树的组成元素，我们也可以获得当前根节点的左子树的大小，因此我们对给定的preorder和inorder进行inplace的切分，并传入下一层递归，对其进行重复的操作并，最终得到当前根节点的左子树，当然，右子树也可以根据类似的步骤得出。

#### Java实现

```Java
class Solution {
    private HashMap<Integer, Integer> map;
    private TreeNode buildTreeHelper(int[] preorder, int[] inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right){
        if(preorder_left > preorder_right){
            return null;
        }
        //得到根节点的位置
        int root_index = map.get(preorder[preorder_left]);
        //新建根节点
        TreeNode root = new TreeNode(preorder[preorder_left]);
        //得到左子树大小
        int size_left_subtree = root_index - inorder_left;
        //递归得到左子树
        root.left = buildTreeHelper(preorder, inorder, preorder_left + 1, preorder_left + size_left_subtree, inorder_left, root_index - 1);
        //递归得到右子树
        root.right = buildTreeHelper(preorder, inorder, preorder_left + size_left_subtree + 1, preorder_right, root_index + 1, inorder_right);
        return root;
        }
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        int n = preorder.length;
        //新建得到HashMap便于得到根节点的位置
        map = new HashMap<Integer, Integer>();
        for (int i = 0; i < n; i++) {
            map.put(inorder[i], i);
        }
        return buildTreeHelper(preorder, inorder, 0, n - 1, 0, n - 1);
    }
}
```

* 时间复杂度：$O(n)$
* 空间复杂度：因为新建了HashMap来储存数组，并且要有$O(n)$​的内存用来返回结果，递归时栈空间是$O(h)$的，由于$h<n$，因此空间复杂度是$O(n)$

### 2.迭代

对于前序遍历任意两个相邻节点p、q，对于这两个节点的关系只有两种可能：

* q是p的左子节点
* p没有左子节点，q是p的某个祖先或p本身的右子节点，因为如果p没有左子节点，按照前序遍历的顺序，下一个遍历的应该是p的右子节点；如果p既没有做子节点又没有右子节点，那么下一个遍历的应该是p的某个祖先的右子节点。

我们可以用一个例子来说明这种解法，假设我们有一颗长成下面这样的树：

```
        a
       / \
      b   c
     /  /  \
    d   e   f
   / \
  g   h
 /
i

前序遍历(preorder)：[a,b,d,g,i,h,c,e,f]
中序遍历(inorder)：[i,g,d,h,b,a,e,c,f]
```

我们维护一个栈，其中储存了所有当前节点还未考虑过右子节点的祖先节点，栈顶便是我们当前的节点。同时，我们记录一个index，其指向中序遍历顺序中第一个有可能有右子节点的节点，初始化成0。

首先我们将根节点入栈，随后我们遍历前序遍历中的节点，判断它是当前节点的左子节点还是当前节点的某个祖先(栈中某个节点)的右子节点在每次将新的节点入栈之前，我们需要检查栈顶的元素是否与index指向的元素的值相同。如果相同，说明这条枝已经到了尽头，下一个节点应该是当前节点的某个祖先节点的右子节点。我们用上面的例子说明。

* 首先将a入栈：

  ```
  stack : [a]
  index : i(inorder[0])
  ```

* 然后将b，d，g，i依次入栈：

  ```
  stack : [a,b,d,g,i]
  index : i(inorder[0])
  ```

* 当我们想要将h入栈时，我们先检查栈顶元素，发现其与index指向的元素的值相同，说明h应该是栈中某个节点的右子节点。这时，我们需要不断从栈中弹出节点，同时将index向后移动，此时从栈中弹出的节点应该与index向后移动后指向的节点相同，因为我们之前的步骤是按从上到下的顺序将整颗二叉树最左边的一枝加到了栈中，由于栈是LIFO的数据结构，我们再从栈中弹出元素，是按从下到上的顺序弹出元素，这与中序遍历中遍历左枝的顺序是相同的。我们一直同步更新它们的值，直到它们不同。因为中序遍历遇见了一个有右子节点的节点，中序遍历会在遍历该节点后遍历其右枝，而我们栈只会不断弹出该节点的父节点。这时我们便发现了左枝中最低的有右枝的节点，这正是h的父节点，此时的stack和index：

  ```
  stack : [a,b,h]
  index : h(inorder[3])
  ```

* 当我们想要c入栈时，情况与h入栈时相同，我们会依次弹出h，b，a，这时`stack.peek()`的结果是`null`，与inorder中下一个元素不同，因此将c入栈，并记为最后弹出的元素a的右子节点：

  ```
  stack : [c]
  index : e(inorder[6])
  ```

* 将e入栈：

  ```
  stack : [c,e]
  index : e(inorder[6])
  ```

* 将f入栈：

  ```
  stack : [f]
  index : f(inorder[8])
  ```

### Java实现

```Java
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        //
        if(preorder == null || preorder.length == 0){
            return null;
        }
        Stack<TreeNode> stack = new Stack<>();
        TreeNode root = new TreeNode(preorder[0]);
        
        stack.push(root);
        int inorderIndex = 0;
        for(int i = 1; i < inorder.length; i++){
            //记下当前前序遍历的值
            int currPreorderValue = preorder[i];
            TreeNode temp = stack.peek();
            //比较当前栈顶节点的值与中序遍历标记的值是否相等
            if(temp.val != inorder[inorderIndex]) {
                temp.left = new TreeNode(currPreorderValue);
                stack.push(temp.left);
            } else{
                //如果不相等则不断弹出节点，知道弹出节点与中序遍历中指针所指节点值不相同
                while(!stack.empty() && stack.peek().val == inorder[inorderIndex]) {
                    temp = stack.pop();
                    inorderIndex++;
                }
                temp.right = new TreeNode(currPreorderValue);
                stack.push(temp.right);
            }
        }
        return root;
    }
}
```

* 时间复杂度：因为我们遍历了有限遍整棵树，因此时间复杂度为$O(n)$
* 空间复杂度：因为新建了一个大小为$n$的树，且新建了一个大小为$O(n)$的栈，迭代所需$O(h)$，$h$为树的高度，$h<n$的空间储存栈，因此空间复杂度为$O(n)$
