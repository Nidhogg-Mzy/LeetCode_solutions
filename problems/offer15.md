# 剑指 Offer 15. 二进制中1的个数

## 题目描述

这是LeetCode上的[剑指 Offer 15. 二进制中1的个数](https://leetcode-cn.com/problems/er-jin-zhi-zhong-1de-ge-shu-lcof/)，难度为简单。



编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数



### 示例1

```
输入：n = 11 (控制台输入 00000000000000000000000000001011)
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```



### 示例2

```
输入：n = 128 (控制台输入 00000000000000000000000010000000)
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
```



### 示例3

```
输入：n = 4294967293 (控制台输入 11111111111111111111111111111101，部分语言中 n = -3）
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```



### 数据范围

- 输入必须是长度为 `32` 的 **二进制串** 。



***

## 解答

### 1. 循环判断每位

分别查看每一位，统计为1的位数即可。




#### **java版：**

```Java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int counter = 0;
        for (int i = 0; i < 32; ++i) {
            if ((n & (1 << i)) != 0) {	// 判断第i位是否为1
                ++counter;
            }
        }
        return counter;
    }
}
```

#### **C++版：**

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int counter = 0;
        for (int i = 0; i < 32; ++i) {
            if (n & (1 << i)) {		// 判断第i位是否为1
                ++counter;
            }
        }
        return counter;
    }
};
```

#### Python3版：

```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        counter = 0;
        for i in range(32): 
            if ((n & (1 << i)) != 0):	# 判断第i位是否为1
                counter += 1
        return counter
```



* 时间复杂度$O(1)$， 因为只循环了31次， 空间复杂度$O(1)$.





### 2. 位运算特性

注意到，$n\&(n - 1)$ 可以将$n$从右往左的第一个$1$变成$0$，例如：

$n=0110\ 1010$，$n\& (n-1)=0110\ 1010\ \&\ 0110\ 1001=0110\ 1000 $,

再算一次就是$0110\ 1000\ \&\ 0110\ 0111 = 0110\ 0000$.

所以我们不断让`n = n & (n - 1)`，直到$n$变为0，这个过程进行了几次，$n$中就有几个1.




#### **java版：**

```Java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int counter = 0;
        while (n != 0) {
            n = n & (n - 1);
            ++counter;		// 每进行一次，计数器加1
        }
        return counter;
    }
}
```

#### **C++版：**

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int counter = 0;
        while (n) {
            n = n & (n - 1);
            ++counter;		// 每进行一次，计数器加1
        }
        return counter;
    }
};
```

#### Python3版：

```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        counter = 0;
        while n != 0: 
            n = n & (n - 1)
            counter += 1	# 每进行一次，计数器加1
        return counter
```

* 时间复杂度$O(1)$，因为最多循环31次， 空间复杂度$O(1)$.



