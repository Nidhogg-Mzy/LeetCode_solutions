# 剑指offer57.和为s的两个数字

## 题目描述

这是LeetCode上的[和为s的两个数字](https://leetcode-cn.com/problems/he-wei-sde-liang-ge-shu-zi-lcof/)， 难度为简单。

输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，则输出任意一对即可。

### 示例1

```
输入：nums = [2,7,11,15], target = 9
输出：[2,7] 或者 [7,2]
```



### 示例2

```
输入：nums = [10,26,30,31,47,60], target = 40
输出：[10,30] 或者 [30,10]
```

### 提示

- $1 \leq$ `nums.length` $\leq 10^5$​​
- $1\leq$ `nums[i]` $\leq 10^6$​​

***

## 解答

### 1. HashMap

遍历一遍数组，对数组中每个元素执行以下操作。在`HashMap`中查找`target - nums[i]`：

* 如果存在，则直接返回`nums[i]`和`target - nums[i]`组成的数组
* 如果不存在，则在`HashMap`中添加该元素，我们只关心`key`值

#### 代码实现

**java版：**

```Java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> map = new HashMap<>();
        for(int i = 0; i < nums.length; i ++){
            if(map.containsKey(target - nums[i])){
                return new int[] {nums[i], target-nums[i]};
            }
            map.put(nums[i], 0);
        }
        return null;
    }
}
```

**C++版：**

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> m;	// hashmap
        vector<int> ans;	// 存储答案
        for (int i = 0; i < nums.size(); ++i) {
            // 这里只有key有用，value是什么无所谓(例如java里面我们让value都为0)
            m[nums[i]] = i;		
        }
        for (auto x : nums) {
            int diff = target - x;
            // 如果找到了，那么find返回的指针不是end()，这时返回答案
            if (m.find(diff) != m.end()) {
                ans.emplace_back(x);
                ans.emplace_back(diff);
                return ans;
            }
        }
        return ans;
    }
};
```

* 时间复杂度$O(n)$, 空间复杂度$O(n)$.





### 2.双指针

由于我们要从数组中找出两个数，所以我们可以用两个指针`i,j`来分别从头和尾开始遍历数组。由于题目保证了数组是递增的，因此如果：

* `nums[i] + nums[j] > target` , 我们就通过减小`j`的值来减小`nums[i] + nums[j]`.
* `nums[i] + nums[j] < target`,  我们就通过增加`i`的值来增加`nums[i] + nums[j]`.
* `nums[i] + nums[j] = target`, 我们就返回包含`i,j`的数组.

#### 代码实现

**java版：**

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int i = 0;
        int j = nums.length - 1;
        while(nums[i] + nums[j] != target){
            if(nums[i] + nums[j] > target){
                j--;
            }
            else if(nums[i] + nums[j] < target){
                i++;
            }
        }
        return new int[] {nums[i], nums[j]};
    }
}
```

**C++版：**

```cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int i = 0, j = nums.size() - 1;
        vector<int> ans;	// ans存储答案
        while (i != j) {	// 一直循环，直到两指针相遇
            int sum = nums[i] + nums[j];
            if (sum == target) {
                ans.emplace_back(nums[i]);
                ans.emplace_back(nums[j]);
                return ans;
            } else if (sum < target) {
                // 如果结果小了，左指针往右走
                ++i;
            } else {
                // 如果结果大了，右指针往左走
                --j;
            }
        }
        return ans;
    }
};
```

* 时间复杂度：$O(n)$​, 空间复杂度$O(1)$.

