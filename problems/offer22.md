# 剑指 Offer 22. 链表中倒数第k个节点

## 题目描述

这是LeetCode上的[链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/), 难度为简单。



输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。

例如，一个链表有 6 个节点，从头节点开始，它们的值依次是 1、2、3、4、5、6。这个链表的倒数第 3 个节点是值为 4 的节点。

### 示例

```
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5.
```



***

## 解答

### 1.暴力遍历

首先遍历一遍链表得到链表的大小，再遍历一遍得到倒数第$k$个节点。

#### 代码实现

```Java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        int size = 0;
        ListNode ptr = head;
        while(ptr != null){
            ptr = ptr.next;
            size ++;
        }
        ptr = head;
        for(int i = 0; i < size - k; i++){
            ptr = ptr.next;
        }
        return ptr;
    }
}
```

* 时间复杂度$O(n)$, 空间复杂度$O(1)$.

### 2. 快慢指针

新建两个指针，让快指针指向第k+1​个`ListNode`, 让慢指针指向第1个`ListNode`, 然后让两个指针同步向后移动，当快指针指向链表尾部`null`时，慢指针的位置即为倒数第k个节点。

### 代码实现

```Java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode fast = head;
        ListNode slow = head;
        for(int i = 0; i < k; i++){
            fast = fast.next;
        }
        while(fast != null){
            fast = fast.next;
            slow = slow.next;
        }
        return slow;
    }
}
```

* 时间复杂度$O(n)$, 空间复杂度$O(1)$.