# 剑指 Offer 06. 从尾到头打印链表

## 题目描述

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

### 实例1

```
输入：head = [1,3,2]
输出：[2,3,1]
```

### 限制

$0 \leq$ 链表长度 $\leq 10000$

***

## 解答

### 1. 暴力

首先遍历一遍链表，得到链表长度，新建一个长度为链表长度的数组，再从尾部向数组中逐个添加元素。

#### Java实现

```Java
class Solution {
    public int[] reversePrint(ListNode head) {
        int length= 0;
        ListNode ptr = head;
        while(ptr != null) {		//遍历数组得到长度
            length ++;
            ptr = ptr.next;
        }
        int[] toReturn = new int[length];
        for(int i = length - 1; i > 0; i--) {		//反向向数组中插入元素
            toReturn[i] = head.val;
            head = head.next;
        }
        return toReturn;
    }
}
```

#### Go实现

````go
func reversePrint(head *ListNode) []int {
    cn := 0
    for p := head; p != nil; p = p.Next {	//遍历数组得到长度
        cn++
    }
    node := make([]int, cn)
    for head != nil {						//反向向数组中插入元素
        node[cn-1] = head.Val
        head = head.Next
        cn--
    }
    return node
}
````

* 由于我们遍历了两遍数组，因此时间复杂度为$O(n)$
* 由于我们新建了一个长度为链表长度的数组，因此空间复杂度为$O(n)$

### 2.递归

首先新建一个`ArrayList`来记倒序的数列。由于我们想要从尾到头打印链表，因此我们在迭代的时候需要先迭代到链表尾部，当检测到尾部空节点的时候再逐层返回，在返回的时候再把每层的数字加到`list`中。再在`reversePrint`函数中把`list`的所有元素加到一个`result`数组中，最后返回`result`数组。

#### Java实现

```Java
class Solution {
    private ArrayList<Integer> list = new ArrayList<>();
    public int[] reversePrint(ListNode head) {
        reversePrintHelper(head);
        int size = list.size();
        int[] result = new int[size];
        for(int i = 0; i < size; i++){
            result[i] = list.get(i);
        }
        return result;
    }
    private void reversePrintHelper(ListNode head){
        if(head == null){		//如果head是尾部空节点直接返回
            return;
        }
        reversePrintHelper(head.next);		//一直向后迭代
        list.add(head.val);		//迭代到最后在返回的时候从下到上向list中加元素
    }
}
```

#### C++实现

```c++
class Solution {
private:
    // 这里我们将存答案的vector作为参数传进去，这里也可以像Java解法一样存为成员变量
    void reverseHelper(ListNode* head, vector<int> &vec) {
        if(head == nullptr) {		//如果head是尾部空节点直接返回
            return;
        }
        // 递归调用，打印从next开始的链表
        reverseHelper(head -> next, vec);
        // 将当前位置的value加入进vector
        vec.push_back(head -> val);
    }
public:
    vector<int> reversePrint(ListNode* head) {
        vector<int> ans;
        reverseHelper(head, ans);
        return ans;
    }
};
```

#### Go实现

```go
func reversePrint(head *ListNode) []int {
    if head == nil{return nil}
    return append(reversePrint(head.Next), head.Val) // return的时候最后一个先return,(recurrsion缩进问题)
}
```

* 由于用递归实现反向插入链表元素，时间复杂度是$O(n)$​，再把`ArrayList`中的元素都放到数组中，时间复杂度也是$O(n)$,因此整个算法时间复杂度是$O(n)$​
* 由于我们新建了一个长度为链表长度的`ArrayList`，因此空间复杂度是$O(n)$​

### 3. Stack

首先新建一个`Stack`，遍历链表，不断向`stack`中`push`节点，由于`stack`是LIFO的数据结构，所以当我们把整个链表`push()`进去之后再不断`pop()`，把所有元素`pop()`到一个新建的数组中，在这个新数组中所有节点的值都是按倒序排序的。

#### Java实现

```Java
class Solution {
    public int[] reversePrint(ListNode head) {
        Stack<ListNode> stack = new Stack<>();
        ListNode temp = head;
        while(temp != null){		//向stack中不断push节点
            stack.push(temp);
            temp = temp.next;
        }
        int size = stack.size();
        int[] arr = new int[size];
        for(int i = 0; i < size; i ++){		//从stack中pop节点，并用arr记录
            arr[i] = stack.pop().val;
        }
        return arr;
    }
}
```

#### C++实现

```c++
class Solution {
public:
    vector<int> reversePrint(ListNode* head) {
        stack<int> s;
        while(head != nullptr) {
            s.push(head -> val);
            head = head -> next;
        }
        // 利用stack的特性，每次pop出来的时候是最后插进去的元素
        vector<int> ans;
        while(!s.empty()){
            ans.emplace_back(s.top());
            s.pop();
        }
        return ans;
    }
};
```

* 因为把所有元素都添加进`stack`和从`stack`把元素都`pop()`出来的时间复杂度是$O(n)$，因此算法时间复杂度为#$O(n)$
* 因为我们新建了一个`stack`，因此空间复杂度为$O(n)$