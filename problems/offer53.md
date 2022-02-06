# 剑指offer53. 0～n-1中缺失的数字

## 题目描述

这是LeetCode上的[0～n-1中缺失的数字](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)， 难度为简单。这道题其实是简单版的[268. 丢失的数字](https://leetcode-cn.com/problems/missing-number/)，题解见[这里](./268.md)，原题是没排序的版本。



给定一个包含 `[0, n]` 中 `n` 个数的数组 `nums` ，找出 `[0, n]` 这个范围内没有出现在数组中的那个数。

### 示例1

```
输入：nums = [3,0,1]
输出：2
解释：n = 3，因为有 3 个数字，所以所有的数字都在范围 [0,3] 内。2 是丢失的数字，因为它没有出现在 nums 中。
```

### 示例2

```
输入：nums = [0,1]
输出：2
解释：n = 2，因为有 2 个数字，所以所有的数字都在范围 [0,2] 内。2 是丢失的数字，因为它没有出现在 nums 中。
```

### 示例3

```
输入：nums = [9,6,4,2,3,5,7,0,1]
输出：8
解释：n = 9，因为有 9 个数字，所以所有的数字都在范围 [0,9] 内。8 是丢失的数字，因为它没有出现在 nums 中。
```

### 示例4

```
输入：nums = [0]
输出：1
解释：n = 1，因为有 1 个数字，所以所有的数字都在范围 [0,1] 内。1 是丢失的数字，因为它没有出现在 nums 中。
```



### 数据范围

- `n == nums.length`
- $1\le n\le 10^4$
- $0\le nums[i]\le n$
- `nums` 中的所有数字都 **独一无二**

***



## 解答

**一般性的题解(即数组中元素未排好序的情况)见[268. 丢失的数字 题解](./268.md)**，这里只列举此题特有的解法。

### 1. 遍历，判断下标与元素值相等

遍历一遍数组，如果下标 $i$ 对应的元素不是`nums[i]`，那一定是数字 $i$ 缺失。注意如果遍历一遍都没找到这样的元素，那一定是数字 $n$ 丢失。

#### 代码实现

**java版：**

```Java
class Solution {
    public int missingNumber(int[] nums) {
        int len = nums.length;
        for (int i = 0; i < len; ++i) {
            // 如果下标与元素不相同，下标位置一定为缺失的元素
            if (i != nums[i]) {
                return i;
            }
        }
        // 如果到这里还没找到，那一定是缺失最后一个元素n
        return len;
    }
}
```

**C++版：**

```cpp
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int length = nums.size();
        for(int i = 0; i < length; ++i) {
            // 如果下标与元素不相同，下标位置一定为缺失的元素
            if(nums[i] != i) {
                return i;
            }
        }
        // 如果到这里还没找到，那一定是缺失最后一个元素n
        return length;
    }
};
```

**Python版：**

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        for i in range(len(nums)):
            # 如果下标与元素不相同，下标位置一定为缺失的元素
            if i != nums[i]:
                return i
        # 如果到这里还没找到，那一定是缺失最后一个元素n
        return len(nums)
```

#### go版：

``` go
func missingNumber(nums []int) int {
    for i:=0;i<len(nums);i++{
        if nums[i] != i {
            return i
        }
    }
    return len(nums)
}
```

* 时间复杂度$O(n)$, 空间复杂度$O(1)$.





### 2.二分查找

由于数组是排好序的，我们可以进行二分搜索：保证`l, r`指向的位置永远是**可能为答案的位置 （注意：这个性质尤为重要，否则边界问题很难处理)**

- 如果`mid`位置有$mid = nums[mid]$，说明答案一定在右侧，注意我们要保证`l`永远指向可能为答案的位置，所以让$l=mid + 1$ 而不是$l=mid$，因为`mid`一定不是答案
- 否则，答案一定在左侧，或者答案就是`mid`，注意我们要保证`r`永远指向**可能为答案的位置**，所以让$r=mid$，而不是`mid-1`，因为`mid-1`不一定为答案。
- 最后当$l$ 与$r$ 相遇的时候，二者所指的位置一定为答案，因为他俩都指向可能为答案的位置，而他俩又指向了同一个元素，所以只有这一个元素是可能的答案。



#### 代码实现

**java版：**

```java
class Solution {
    public int missingNumber(int[] nums) {
        int l = 0, r = nums.length;
        // 当 l 和 r 相遇的时候结束
        while(l < r) {
            int mid = (l + r) >> 1;
            if(nums[mid] == mid) {
                l = mid + 1;	// 答案一定在mid右侧，mid一定不是答案
            } else {
                r = mid;	// 答案在mid或者mid的左侧
            }
        }
        return r;	// 此时l和r指向同一个位置，返回任何一个均可
    }
}
```

**C++版：**

```cpp
class Solution {
public:
    int missingNumber(vector<int>& nums) {
        int l = 0, r = nums.size();
        // 当 l 和 r 相遇的时候结束
        while(l < r) {
            int mid = (l + r) >> 1;
            if(nums[mid] == mid) {
                l = mid + 1;	// 答案一定在mid右侧，mid一定不是答案
            } else {
                r = mid;	// 答案在mid或者mid的左侧
            }
        }
        return r;	// 此时l和r指向同一个位置，返回任何一个均可
    }
};
```

**Python版：**

```python
class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        l = 0
        r = len(nums)
        # 当 l 和 r 相遇的时候结束
        while l < r: 
            mid = (l + r) >> 1
            if nums[mid] == mid: 
                l = mid + 1		# 答案一定在mid右侧，mid一定不是答案
            else:
                r = mid			# 答案在mid或者mid的左侧
        return r		# 此时l和r指向同一个位置，返回任何一个均可
```

#### Go版：

```go
func missingNumber(nums []int) int {
    left := 0
    right := len(nums)
    for left < right {
        mid := (left + right) >> 1
        if nums[mid] == mid {
            left = mid + 1
        } else {
            right = mid
        }
    }
    return right
}
```

* 时间复杂度：$O(\log n)$​, 空间复杂度$O(1)$.

### 3. 数学公式

#### Go版

``` go
func missingNumber(nums []int) int {
    total := (1+len(nums))*len(nums)/2
    count:=0
    for i:=0;i<len(nums);i++{
        count = count + nums[i]
    }
    return total - count
}
```



