# 剑指 Offer 24. 反转链表

## 题目描述

这是LeetCode上的[反转链表](https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/), 难度为简单

定义一个函数，输入一个链表的头节点，反转该链表并输出反转后链表的头节点。

### 示例

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
```

### 限制

$0 \leq$ 节点个数 $\leq 5000$

***

## 解答

### 1.迭代

定义`curr`和`prev`来分别记下当前节点和前一个节点。当当前节点不为尾部空节点时，则不断向后循环，每次循环内记下`curr`的下一个节点，把`curr`当前节点的`next`指向`prev`，然后更新`curr`和`prev`的值。最后返回`prev`

#### Java实现

```Java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode curr = head;
        ListNode prev = null;
        while(curr != null){
            ListNode temp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = temp;
        }
        return prev;
    }
}
```

#### C++实现

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        ListNode* p = nullptr;  // iterator pointer
        ListNode* prev = nullptr;   // previous pointer
        while(head != nullptr) {
            prev = p;
            p = new ListNode(head -> val, prev);
            head = head -> next;
        }
        return p;
    }
};
```

* 因为我们遍历了一遍链表，时间复杂度为$O(n)$​
* 因为我们只新建了3个用来记node的节点，因此空间复杂度为$O(1)$

### 2. 递归

首先设置递归终点和特殊情况判定，如果`head`或`head`的`next`为`null`，则返回`head`。之后一直向后递归，首先移动到链表尾部，把尾部节点的`next`指向他的前一个节点，以此类推，最后返回`newNode`

#### Java实现

```Java
class Solution {
    public ListNode reverseList(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        ListNode newNode = reverseList(head.next);
        head.next.next = head;
        head.next = null;
        return newNode;
    }
}
```

#### C++实现

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (head == nullptr || head -> next == nullptr) {
            return head;
        }
        ListNode* new_head = reverseList(head -> next);
        head -> next -> next = head;
        head -> next = nullptr;
        return new_head;
    }
};
```

* 时间复杂度为$O(n)$，n为链表长度
* 空间复杂度为$O(n)$，n为链表长度

