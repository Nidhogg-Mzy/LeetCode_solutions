# 剑指 Offer 52. 两个链表的第一个公共节点

## 題目描述

这是LeetCode上的[两个链表的第一个公共节点](https://leetcode-cn.com/problems/liang-ge-lian-biao-de-di-yi-ge-gong-gong-jie-dian-lcof/), 难度为简单

输入两个链表，找出它们的第一个公共节点。

如下面的两个链表**：**

<img src="images\image-20220125204913343.png" alt="image-20220125204913343" style="zoom:80%;" />

在节点 c1 开始相交。

### 示例1

<img src="images\image-20220125205124175.png" alt="image-20220125205124175" style="zoom:80%;" />

```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
```

### 示例2

<img src="images\image-20220125205203765.png" alt="image-20220125205203765" style="zoom:80%;" />

```
输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Reference of the node with value = 2
输入解释：相交节点的值为 2 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
```

### 示例3

<img src="images\image-20220125205243884.png" alt="image-20220125205243884" style="zoom:80%;" />

```
输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
输入解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
解释：这两个链表不相交，因此返回 null。
```

### 注意

* 如果两个链表没有交点，返回 null.
* 在返回结果后，两个链表仍须保持原有的结构。
* 可假定整个链表结构中没有循环。
* 程序尽量满足 O(n) 时间复杂度，且仅用 O(1) 内存。
* 本题与主站 [160 题](https://leetcode-cn.com/problems/intersection-of-two-linked-lists/)相同

***

## 解答

### 1.HashSet

首先遍历一遍链表A, 将其中的所有节点添加到HashSet中，再遍历链表B，对每个B中节点再HashSet中进行查找，如果已存在，则直接返回，即为两链表第一个公共节点；如果遍历一遍后仍没有，则返回空值。

#### Java实现

```Java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        HashSet<ListNode> set = new HashSet<>();
        ListNode ptrA = headA;
        while(ptrA != null){
            set.add(ptrA);
        }
        ListNode ptrB = headB;
        while(ptrB != null){
            if(set.contains(ptrB)){
                return ptrB;
            }
        }return null;
    }
}
```

#### C++实现

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        unordered_set<ListNode*> set;
        while (headA != nullptr) {
            set.insert(headA);
            headA = headA -> next;
        }
        while (headB != nullptr) {
            if (set.find(headB) != set.end()) {
                return headB;
            }
            headB = headB -> next;
        }
        return nullptr;
    }
};
```


* 时间复杂度：因为我们最坏情况下会遍历一遍A链表和B链表，因此时间复杂度为$O(m+n)$，其中m是A的长度，n是B的长度
* 空间复杂度：因为我们新建了一个HashSet来储存A中的所有节点，因此空间复杂度为$O(m)$，其中m是A的长度

### 2. 双指针

首先进行特殊情况判断，如果两个链表有任意一个是空值，则他们不可能有交点，可以直接返回空值。新建两个指针分别指向两个链表的头部，同时让这两个指针向后遍历知道这两个指针的值相等。如果指针A指向空值，即已经遍历到链表结尾，那么让其指向链表B的开始，继续遍历，对于指针B进行同样的操作。

我们现在来证明算法的正确性。设链表A、B的公共部分长度为c，A链表除了公共部分长度为a，B链表除了公共部分长度为b。如果A、B链表有公共节点，那么遍历一遍后，指针A走过了$a+c$​​​​的距离，指针B走过了$b+c$​​​的距离，指针A会从B的头部开始继续遍历，指针B会从A的头部开始遍历，当它们在第二圈遍历是经过第一个公共节点的时候，指针A走过了$a+c+b$​的距离，同时指针B也走过了$a+c+b$​的距离，指针A与指针B指向同一个节点，跳出循环，返回第一个公共节点；如果A、B链表没有公共节点，那么在两个指针分别在本来的链表上遍历一遍后，再到对方的链表上遍历一遍，它们同时走过了$a+b+2c$的距离，因此它们同时指向了空值，跳出循环，返回空值。

#### Java实现

```Java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if(headA == null || headB == null){
            return null;
        }
        ListNode ptrA = headA;
        ListNode ptrB = headB;
        while(ptrA != ptrB){
            ptrA = ptrA != null ? ptrA.next : headB;
            ptrB = ptrB != null ? ptrB.next : headA;
        }
        return ptrA;
    }
}
```

#### C++实现

```cpp
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* ptrA = headA;
        ListNode* ptrB = headB;
        while(ptrA != ptrB){
            ptrA = ptrA != nullptr ? ptrA -> next : headB;
            ptrB = ptrB != nullptr ? ptrB -> next : headA;
        }
        return ptrA;
    }
};
```

* 时间复杂度：因为最坏情况下，我们会把A、B链表遍历一遍，因此时间复杂度为$O(m+n)$​​，其中$m$​​为链表A的长度，$n$为链表B的长度

* 空间复杂度：因为我们只新建了两个指针，因此空间复杂度为$O(1)$

