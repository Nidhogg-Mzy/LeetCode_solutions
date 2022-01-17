# 剑指 Offer 30. 包含min函数的栈

## 题目描述

这是LeetCode上的[包含min函数的栈](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/), 难度为简单。



定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，调用 min、push 及 pop 的时间复杂度都是 $O(1)$。

### 示例

```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.min();   --> 返回 -3.
minStack.pop();
minStack.top();   --> 返回 0.
minStack.min();   --> 返回 -2.
```



### 数据范围

- 各函数的调用总次数不超过 20000 次



***

## 解答

考虑一组数$A=[3,7,10,2,5]$, 在这时候最小值为2，如果我们将5弹出，最小值仍为2，当时如果我们再弹出一个元素2，变为$A=[3,7,10]$，此时最小值就变成了3。

我们会发现，之所以将5弹出的时候，最小值仍为2，是因为2“**统治了**”5这个元素，也就是说，只要有2在，不管有没有5，最小值就一定是2。同样道理，当$A=[3,7,10]$时，3“**统治了**”7、10两个元素，只要3还在，7和10无法决定最小值。

这就是这道题基本的思路，要实现这个算法，有几种方式：

### 1. 将最小值信息放入栈的结点

我们考虑将每个栈的结点存入两个值`<val, min>`，`val`记录这一结点的值，`min`记录这一结点还在时的最小值，也就是说我在求最小值的时候，只要看栈顶元素的`min`是多少就可以了。举例来说，数组$[3,7,10,2,5]$，会存成

$[<3,3>, <7,3>, <10,3>, <2,2>, <5,2>]$, 每个点分别对应`<val, min>`

那么每次在`min()`操作时，我们只要返回栈顶元素的`<min>`值，在`top()`操作时，只要返回栈顶元素的`<val>`值。

现在考虑如何实现`push()`操作，如果我们现在要加入一个$x$，那么$x$的`<min>`值要么是$x$本身，要么是它前面元素的`<min>`值（想想为什么）, 这是由`<min>`值的定义决定的。所以插入的时候，`x.min = min(x, top.min)`.



#### 代码实现

**Java 版：**

```Java
class MinStack {
    // 新建一个Node类，表示栈中的每个结点
    private class StackNode {
        private int val, min;
        public StackNode(int val, int min) {
            this.val = val;
            this.min = min;
        }
    }
    private Stack<StackNode> s;
    /** initialize your data structure here. */
    public MinStack() {
        s = new Stack<>();
    }
    
    public void push(int x) {
        int newMin = x;
        // 取 x 和 栈顶元素的min 二者较小的那个
        if(!s.isEmpty()) {
            newMin = Math.min(newMin, s.peek().min);
        }
        s.push(new StackNode(x, newMin));
    }
    
    public void pop() {
        s.pop();
    }
    
    public int top() {
        return s.peek().val;
    }  
    
    public int min() {
        return s.peek().min;
    }
}
```

**C++版：**

```cpp
class MinStack {
private:
    // 每个结点存value和min
    struct StackNode {
        int value, min;
    };
    stack<StackNode> s;
public:
    /** initialize your data structure here. */
    MinStack() {}
    
    void push(int x) {
        int newMin = x;
        // 取 x 和 栈顶元素的min 二者较小的那个
        if(!s.empty()) {
            newMin = std::min(newMin, s.top().min);
        }
        // 将x和newMin打包成一个StackNode传入
        s.push((StackNode){x, newMin});
    }
    
    void pop() {
        s.pop();
    }
    
    int top() {
        return s.top().value;
    }
    
    int min() {
        return s.top().min;
    }
};
```

* 时间复杂度均为$O(1)$，符合题目要求； 空间复杂度$O(n)$，因为新建了一个与原数组等长的栈，栈中的每个元素为2个整数。



### 2. 用额外的栈存最小值

与方法1比较类似，这次每个结点仍然是整数，但是我们用一个额外的“**同步的**”栈存储当前位置以及之前的最小值。例如，数组$[3,7,10,2,5]$，会存成：

`valueStack`: $[3,7,10,2,5]$

`minStack`: $[3,3,3,2,2]$

同样还是要对`push()`操作进行修改，每次不光要把元素放进`valueStack`，还要取元素和`minStack`栈顶的元素二者较小的那个，放入`minStack`中。（想想为什么，与解法1类似）



#### 代码实现

**Java 版：**

```Java
class MinStack {
    private Stack<Integer> valStack;	// 存原值的栈
    private Stack<Integer> minStack;	// 存对应最小值的栈
    
    /** initialize your data structure here. */
    public MinStack() {
        valStack = new Stack<>();
        minStack = new Stack<>();
    }
    
    public void push(int x) {
        // 将x弹入value栈中
        valStack.push(x);
        
        int newMin = x;
        // 取 x 和 最小值栈的栈顶 二者较小的那个
        if(!minStack.isEmpty()) {
            newMin = Math.min(newMin, minStack.peek());
        }
        minStack.push(newMin);
    }
    
    public void pop() {
        // 每次pop要同时弹出两个栈的元素，保持二者大小相等
        valStack.pop();
        minStack.pop();
    }
    
    public int top() {
        return valStack.peek();
    }  
    
    public int min() {
        return minStack.peek();
    }
}
```

**C++版：**

```cpp
class MinStack {
private:
    stack<int> valueStack;	// 存原值的栈
    stack<int> minStack;	// 存对应最小值的栈
public:
    /** initialize your data structure here. */
    MinStack() {}
    
    void push(int x) {
        // 将x弹入value栈中
        valueStack.push(x);
        
        int newMin = x;
        // 取 x 和 最小值栈的栈顶 二者较小的那个
        if(!minStack.empty()) {
            newMin = std::min(newMin, minStack.top());
        }
        minStack.push(newMin);
    }
    
    void pop() {
        // 每次pop要同时弹出两个栈的元素，保持二者大小相等
        valueStack.pop();
        minStack.pop();
    }
    
    int top() {
        return valueStack.top();
    }
    
    int min() {
        return minStack.top();
    }
};
```

* 时间复杂度均为$O(1)$，符合题目要求； 空间复杂度$O(n)$，因为新建了两个与原数组等长的栈。





### 3. 只有在最小值改变的时候进行记录

在方法2中，我们说数组$[3,7,10,2,5]$会存成：

`valueStack`: $[3,7,10,2,5]$

`minStack`: $[3,3,3,2,2]$

发现`minStack`中有很多冗余的元素。考虑去除重复的元素，将其优化成这样：

`minStack`: $[3,2]$

我们一开始说，2**统治了**5，那我们在弹出5的时候，不将`minStack`中的元素弹出，只有发现`minStack`和`valueStack`的栈顶元素相同的时候，也就是如果此时弹出元素，最小值就改变的时候，将`minStack`中的元素弹出。

这里要注意一下如果有重复元素的问题，例如`valueStack`: $[3,4,3]$，那它的`minStack`应该为$[3,3]$，而不是只有一个$[3]$，不然我们在第一次弹出的时候，就会把3从`minStack`弹出，这显然是我们不想要的。这就提示我们，在`push()`的时候，只要给定的新元素**大于等于**`minStack`栈顶的元素，我们就要将它加进去。



#### 代码实现

**Java 版：**

```Java
class MinStack {
    private Stack<Integer> valStack;	// 存原值的栈
    private Stack<Integer> minStack;	// 存最小值的栈
    
    /** initialize your data structure here. */
    public MinStack() {
        valStack = new Stack<>();
        minStack = new Stack<>();
    }
    
    public void push(int x) {
        valStack.push(x);
        
        // 这里是 <= , 为了防止有重复元素的时候(见上面的解释)
        if(minStack.isEmpty() || x <= minStack.peek()) {
            minStack.push(x);
        }
    }
    
    public void pop() {
        int valTop = valStack.pop();
        // 如果两栈栈顶的元素相同，才要把minStack栈顶弹出
        if (minStack.peek() == valTop) {
            minStack.pop();
        }
    }
    
    public int top() {
        return valStack.peek();
    }  
    
    public int min() {
        return minStack.peek();
    }
}
```

**C++版：**

```cpp
class MinStack {
private:
    stack<int> valueStack;	// 存原值的栈
    stack<int> minStack;	// 存最小值的栈
public:
    /** initialize your data structure here. */
    MinStack() {}
    
    void push(int x) {
        valueStack.push(x);
        
        // 这里是 <= , 为了防止有重复元素的时候(见上面的解释)
        if(minStack.empty() || x <= minStack.top()) {
            minStack.push(x);
        }
    }
    
    void pop() {
        int val = valueStack.top();
        valueStack.pop();
        // 如果两栈栈顶的元素相同，才要把minStack栈顶弹出
        if(minStack.top() == val) {
            minStack.pop();
        }
    }
    
    int top() {
        return valueStack.top();
    }
    
    int min() {
        return minStack.top();
    }
};
```

* 时间复杂度均为$O(1)$，符合题目要求； 空间复杂度$O(n)$，但是实际上比上面两种解法空间少一些。