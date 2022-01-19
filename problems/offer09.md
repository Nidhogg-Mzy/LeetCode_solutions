# 剑指 Offer 09. 用两个栈实现队列

## 题目描述

这是LeetCode上的[用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/), 难度为简单。



需要实现队列的两个功能`appendTail` 和` deleteHead` ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，`deleteHead` 操作返回 -1 )



### 示例

```
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]


输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```



### 数据范围

- `1 <= values <= 10000`

- `最多会对 appendTail、deleteHead 进行 10000 次调用`

  

***

## 解答

插入元素时，直接将元素插入到栈$A$的顶部。接下来考虑删除的时候，队列每次删除要弹出队列首的元素，也就是**第一个插入**的元素，它应该在我们栈的**最底部**。这样的话我们需要把栈$A$中所有的元素都弹出来，弹入$B$中，此时我们就把$A$中元素的顺序倒过来了，也就是$A$栈底的元素变成了$B$栈顶的元素；同理，$A$栈底的第二个元素变成了$B$栈顶的第二个元素。

这样，我们每次删除的时候检查$B$中是否有元素：如果有，就直接弹出$B$栈顶的元素；如果没有，就将$A$中所有的元素弹出到$B$中，再从$B$向外弹，如果$B$还是空的，那么就说明在没有元素的情况下要求delete，那么返回 -1.

在代码中，我们将栈$A$称为`val`栈，将栈$B$称为`del`栈，原因应该不难理解吧！



#### 代码实现

**Java 版：**

```Java
class CQueue {
    private Stack<Integer> val;
    private Stack<Integer> del;
    
    public CQueue() {
        val = new Stack<>();
        del = new Stack<>();
    }
    
    public void appendTail(int value) {
        val.add(value);
    }
    
    public int deleteHead() {
        // 如果 <del> 不为空，直接弹出栈顶
        if(!del.isEmpty()) {
            return del.pop();
        }
        // 将 <val> 中所有元素弹入 <del> 中
        while(!val.isEmpty()) {
            del.add(val.pop());
        }
        // 如果 <del> 仍为空，返回-1，否则返回栈<del>顶元素
        return del.isEmpty() ? -1 : del.pop();
    }
}
```

**C++版：**

```cpp
class CQueue {
private:
    stack<int> val;
    stack<int> del;

public:
    CQueue() {
        
    }
    
    void appendTail(int value) {
        val.push(value);
    }
    
    int deleteHead() {
        // 如果 <del> 不为空，直接弹出栈顶
        if(!del.empty()) {
            int toReturn = del.top();
            del.pop();	// cpp中的pop()函数是void类型，不返回栈顶元素
            return toReturn;
        }
        // 将 <val> 中所有元素弹入 <del> 中
        while(!val.empty()) {
            del.push(val.top());
            val.pop();
        }
        // 如果 <del> 仍为空，返回-1，否则返回栈<del>顶元素
        if(del.empty()) {
            return -1;
        } else {
            int toReturn = del.top();
            del.pop();
            return toReturn;
        }
    }
};
```

**C++版：**

```go
type CQueue struct {
     stack1 *list.List
     stack2 *list.List
}


func Constructor() CQueue {
	return CQueue{
		stack1: list.New(),
		stack2: list.New(),
	}
}


func (this *CQueue) AppendTail(value int)  {
	this.stack1.PushBack(value)
}


func (this *CQueue) DeleteHead() int {
//如果第二个栈为空的情景
    if this.stack2.Len() == 0{
        for this.stack1.Len() >0{
            //Remove删除链表中的元素e，并返回e.Value
            //Back返回链表最后一个元素或nil
            //PushBack将一个值为v的新元素插入链表的最后一个位置，返回生成的新元素。
            this.stack2.PushBack(this.stack1.Remove(this.stack1.Back()))
        }
    }
    if this.stack2.Len() !=0{
        e :=this.stack2.Back()
        this.stack2.Remove(e)
        //e.value是interface{}类型，最终返回的是int类型，给他强转
        return e.Value.(int)
    }
    return -1
}
```

* 对于每次插入，时间复杂度显然为$O(1)$；对于删除，每次操作的**均摊复杂度为**$O(1)$，可以考虑每个元素最多会被从`del`栈弹入和弹出一次，或者考虑成如果你某一次将$k$个数从`val`栈弹入`del`栈，那接下来的$k$个数都有$O(1)$的删除复杂度，将$k$的复杂度均摊给这$k$个数，就相当于每个元素删除用了2次操作，也就是$O(1)$.
* 空间复杂度$O(n)$, 因为我们开了两个栈，他们的容量加起来就是整数的个数$n$.
