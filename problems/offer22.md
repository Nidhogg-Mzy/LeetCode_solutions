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

**Java 版：**

```Java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        int size = 0;
        ListNode ptr = head;
        while(ptr != null){		//遍历一遍数组得到size
            ptr = ptr.next;
            size ++;
        }
        ptr = head;
        for(int i = 0; i < size - k; i++){		//遍历第二遍得到第k个节点
            ptr = ptr.next;
        }
        return ptr;
    }
}
```

**C++版：**

```cpp
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        int length = 0;
        ListNode* p = head;
        // 遍历一遍得到size
        while (p != nullptr) {
            ++length;
            p = p -> next;
        }
        int counter = 0;
        p = head;
        while (counter++ < length - k) {
            p = p -> next;
        }
        return p;
    }
};
```

**Go版：**

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func getKthFromEnd(head *ListNode, k int) *ListNode {
    n := 0
    node, k_th := head, head
    for ; node != nil; node = node.Next {
        n++
    }
    for ; n > k; n-- {
        k_th = k_th.Next
    }
    return k_th
}
```

* 时间复杂度$O(n)$, 空间复杂度$O(1)$.

### 2. 快慢指针

新建两个指针，让快指针指向第k+1​个`ListNode`, 让慢指针指向第1个`ListNode`, 然后让两个指针同步向后移动，当快指针指向链表尾部`null`时，慢指针的位置即为倒数第k个节点。

### 代码实现

**java版：**

```Java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode fast = head;
        ListNode slow = head;
        for(int i = 0; i < k; i++){		// 让快指针指向第k+1个ListNode
            fast = fast.next;
        }
        while(fast != null){		// 同时update快慢指针的值
            fast = fast.next;
            slow = slow.next;
        }
        return slow;
    }
}
```

**C++版：**

```cpp
class Solution {
public:
    ListNode* getKthFromEnd(ListNode* head, int k) {
        ListNode* fast = head, *slow = head;
        while (fast != nullptr && k-- > 0) {
            fast = fast -> next;
        }
        // 此时快指针指向第k+1个节点，因此快指针比慢指针提前了k个点
        // 快慢指针共同向后，当快指针到达尾部时，慢指针恰好是所求的节点
        while (fast != nullptr) {
            fast = fast -> next;
            slow = slow -> next;
        }
        return slow;
    }
};
```

**Go版：**

```go
/**
 * Definition for singly-linked list.
 * type ListNode struct {
 *     Val int
 *     Next *ListNode
 * }
 */
func getKthFromEnd(head *ListNode, k int) *ListNode {
    fast, slow := head, head
    for fast != nil && k > 0 {
        fast = fast.Next
        k--
    }   // move the fast pointer to k_th place正向第k个
    for fast != nil {
        fast = fast.Next    // fast 一直走到尽头，此时走了正向n-k个
        slow = slow.Next    // slow 此时同时走正向n-k个，为倒数第k个节点，return即可
    }
    return slow
}
```

* 时间复杂度$O(n)$, 空间复杂度$O(1)$​.

### 3. Stack

新建一个`Stack`, 然后把所有的节点插进去，然后再从其头部弹出k个节点。

#### 代码实现

```Java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        Stack<ListNode> stack = new Stack<ListNode>();
        ListNode ptr = head;
        while(ptr != null){
            stack.push(ptr);
            ptr = ptr.next;
        }
        for(int i = 0; i < k - 1; i++){
            stack.pop();
        }
        return stack.pop();
    }
}
```

* 时间复杂度$O(n)$​, 空间复杂度$O(n)$.





### 4. Queue

新建一个`Queue`，把所有节点插进去，同时记下`Queue`的$size$, 然后再从头部弹出$size - k + 1$个节点。

#### 代码实现

```Java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        Queue<ListNode> queue = new LinkedList<ListNode>();
        ListNode ptr = head;
        while(ptr != null){
            queue.add(ptr);
            ptr = ptr.next;
        }
        int size = queue.size(); // 需要一个变量来存queue的size因为每次poll()之后size会变
        for(int i = 0; i < size - k; i++){
            queue.poll();
        }
        return queue.poll();
    }
}
```

* 时间复杂度$O(n)$,空间复杂度$O(n)$​.