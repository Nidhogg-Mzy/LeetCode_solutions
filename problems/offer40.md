# 剑指offer40.最小的k个数

## 题目描述

这是LeetCode上的[最小的k个数](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/)，难度为简单。

输入整数数组 `arr` ，找出其中最小的 `k` 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

### 示例1

```
输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
```

### 示例2

```
输入：arr = [0,1,2,1], k = 1
输出：[0]
```

### 限制

- $0 \leq k \leq$​ `arr.length` $\leq 10000$​​​​
- $0\leq$​ `arr[i]` $\leq 10000$​​​

***

## 解答

### 1.计数

由于k的数据范围是$0 \leq k \leq$ `arr.length` $\leq 10000$​， 所以我们需要新建一个大小为$10001$​的数组来记下每个数字出现的次数。遍历之后，再新建一个数组，长度为k，用来记录需要被返回的k个数，新建一个`index`来记当前`result`数组中数字个数。再遍历`count`数组，​同时我们检查如果插入所有当前元素，`result`数组是否会超出大小上限`k`：如果会，则从`index`到`k`全部填满当前元素；如果不会，则从`index`到`index+count[i]`全部填满当前元素。最后返回`result`数组。

#### Java实现

```Java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        int[] count = new int[100001];
        for(Integer num : arr){		//在数组中记下每个数字出现了几次
            count[num]++;
        }
        int index = 0;
        int[] result = new int[k];
        for(int i = 0; i < count.length; i++){
            if(index + count[i] > k){
                for(int j = index; j < k; j++){
                    result[j] = i;
                }
                break;
            }
            else{
                for(int j = index; j < index + count[i]; j++){
                    result[j] = i;
                }
                index += count[i];
            }
        }
        return result;
    }
}
```

* 时间复杂度：$O(n+k)$​​​， 因为遍历了一遍给定数组，又进行了K次遍历来填充要返回的数组。
* 空间复杂度：$O(k)$​， 新建了一个大小为k的数组来记下结果。

### 2. TreeMap

首先进行特殊情况判断，如果`k==0`或者`arr`长度为0，直接返回空数组。我们新建一个`TreeMap`, 用`count`来记录当前`map`中包含的总元素个数。由于`TreeMap`的`comparator`是比较`key`的值，因此我们只需要维护一个`count`为`k`的`TreeMap`就可以保证我们得到最小的K个数。具体步骤为：遍历整个数组，如果`count < k`且存在`map`中当前`key`，则把当前元素的对应的`value`增加1；反之如果`map`中不存在`num` 则把`num`的`value`设为0并`put`进去。如果`count > k`，则比较当前`num`与`map`中最大的`key`的值的大小关系,如果`num`大于 `map`中最大值，则跳过；如果小于，则把当前`num`插入`map`中，并把当前`map`中最大值的数量减少1，以此类推。

#### Java实现

```Java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if(k == 0 || arr.length == 0){		//特殊情况判定
            return new int[0];
        }
        TreeMap<Integer, Integer> map = new TreeMap<>();
        int count = 0;
        for(Integer num : arr){
            if(count < k){
                map.put(num, map.getOrDefault(num, 0) + 1);
                count++;
            }
            else{
                Map.Entry<Integer, Integer> entry = map.lastEntry		//得到当前map中的最后一个元素
                if(num < entry.getKey()){
                    map.put(num, map.getOrDefault(num, 0) + 1);
                    if(entry.getValue() == 1){		//如果最后一个元素只有一个，则直接poll
                        map.pollLastEntry();
                    }
                    else{
                        map.put(entry.getKey(), entry.getValue() - 1);		//如果不止一个，则把它的值减一
                    }
                }
            }
        }
        int[] result = new int[k];		//将map转化为数组返回
        int index = 0;
        for (Map.Entry<Integer, Integer> entry: map.entrySet()) {
            int freq = entry.getValue();
            while (freq-- > 0) {
                result[index++] = entry.getKey();
            }
        }
        return result;
    }
}
```

* 时间复杂度：因为我们一直维护一个size为k的`TreeMap`，因此其高度为$logk$，最坏情况是对于给定数据每个都进行一次插入，每次插入的时间复杂度为$O(logk)$，因此整体时间复杂度为$O(Nlogk)$
* 空间复杂度：因为新建了一个大小为$k$​​的`TreeMap`，因此空间复杂度为$O(k)$

### 3. 最大堆

#### Java实现

首先判断特殊情况，即为`k`或`arr`的长度为0的时候，直接返回空数组。由于我们在每次得到新的元素并入堆时，我们需要将其与当前堆中最大的元素比较，所以我们选择维护一个大小为`k`的`max-heap`。使用Java中的`PriorityQueue`来实现`max-heap`需要重载`comparator`，因为`java`中默认是`min-heap`。剩下只需要维护这个`max-heap`就行了，思路与上面二叉树实现类似，就不赘述了。

```Java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if(k == 0 || arr.length == 0){
            return new int[0];
        }
        Queue<Integer> pq = new PriorityQueue<>((v1,v2) -> v2 - v1);		//重载comparator
        for(Integer num : arr){
            if(pq.size() < k){		//如果当前堆大小小于k，则继续向堆内加元素
                pq.add(num);
            }
            else{
                if(num < pq.peek()){		//比较堆顶元素与当前元素的大小关系
                    pq.add(num);
                    pq.poll();
                }
            }
        }
        int[] res = new int[k]; 	//返回一个数组
        int index = 0;
        for(int num: pq) {
            res[index++] = num;
        }
        return res;
    }
}
```

#### Go实现

```go
func getLeastNumbers(arr []int, k int) []int {
    if k==0{
        return []int{}
    }

    heap := arr[:k]
    for i:= k;i >=0 ;i--{
        heapify(heap,i)
    }

    // 从输入的第k个元素开始进行比较，如果小于最大堆的堆顶则
    // 认为这个元素是TopK小的元素
	for i:=k;i<len(arr);i++{
        if arr[i] < heap[0]{
            heap[0] = arr[i]
            heapify(heap,0)
        }
    }
    return heap
}


func heapify(arr []int,k int)[]int{
    max := 0 
    size := len(arr)
    for k < size {
        max = k
        n := 2 * k
        if n + 1 < size && arr[n+1] > arr[max] {
            max = n + 1
        }
        if n + 2  < size && arr[n+2] > arr[max] {
            max = n + 2
        }
        if max == k {
            break
        }
        arr[k],arr[max] = arr[max],arr[k]
        k = max
    }
    return arr
}
```



* 时间复杂度：因为最坏情况是要对数组中每个元素都进行一次在大小为k的堆中的插入，因此时间复杂度为$O(Nlogk)$​​ 。
* 空间复杂度：因为新建了大小为k的堆，因此空间复杂度为$O(k)$

### 4. 快速排序(Dual-pivot quickSort)

首先进行特殊情况判断，当k大于给定数组长度时，直接返回给定数组。对给定数组进行快速排序，每次新建指向区间开始和结束的两个指针left和right，选定数组第一个元素为快速排序切分的pivot。让right一直向前移动直到遇见第一个小于pivot的元素，left一直向后移动直到遇见第一个大于pivot的元素，交换这两个元素（这样就实现了小于pivot的元素位于数组前方，大于pivot的元素位于pivot后方），一直进行这个操作，直到在区间开始和结束的两个指针相遇。这时交换区间开头的元素和left指向的元素。这时left的大小代表了数组小于pivot和一部分等于pivot的元素的数量和，right的大小代表了数组中大于pivot和一部分等于pivot的元素的数量和，换句话说，**left那一部分的元素$<$​pivot $< $​​​​right那一部分的元素**。如果left$<$​k，则说明我们还未找到k个最小的元素，则我们需要继续对right那部分进行`quickSort`，如果left$>$​​k则对left那部分进行`quickSort`，如果left$=$​k则直接返回数组前k个数组成的数组。

#### Java实现

```Java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if(k >=arr.length){		//特殊情况判定
            return arr;
        }
        return quickSort(arr, k, 0, arr.length - 1);
    }
    private void swap(int[] arr, int i, int j) {		//helper method帮助我们交换数组中两个值
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    private int[] quickSort(int[] arr, int k, int start, int end) {
        int left = start;
        int right = end;
        int pivot = arr[start];		//选定一开始的节点为pivot
        while(left < right) {
            while(left < right && arr[right] >= pivot) {		//right指针一直向左移动直到遇见小于pivot的元素
                right--;
            }
            while(left < right && arr[left] <= pivot) {		//left指针一直向有移动直到遇见大于pivot的元素
                left++;
            }
            swap(arr, left, right);
        }
        swap(arr, start, left);
        if(left > k){		//如果left部分大于k个元素，则对left那部分进行quickSort
            return quickSort(arr, k, start, left - 1);
        }
        if(left < k){		//如果left部分没到k个元素，则对right那部分进行quickSort
            return quickSort(arr, k, left + 1, end);
        }
        return Arrays.copyOf(arr, k);		//返回前k个元素组成的数组
    }
}
```

#### Go实现

```go
func quicksort(arr []int) []int {
    if len(arr) < 2 {
        return arr
    }
    left := []int{}
    right := []int{}
    for _,value := range arr[1:] {  // 假设第一项为中间项，遍历第一项之后的数组
        if value > arr[0] {
            right = append(right, value) // 将大于第一项的数加到right数组里
        } else {
            left = append(left, value)  // 将小于第一项的数加到left数组里 
        }
    } 
    return append(quicksort(left),append([]int{arr[0]},quicksort(right)...)...) // 递归调用，直到排好顺序
}

func getLeastNumbers(arr []int, k int) []int {
    array := quicksort(arr) // 排好序之后的数组
    return array[:k]
}
```

* 时间复杂度：由于向下递归的子数组平均长度为$\frac{N}{2}$​​​，因此时间复杂度为$N+\frac{N}{2}+\frac{N}{4}+\cdots \frac{N}{N} = 2N-1$​,因此时间复杂度为$O(n)$
* 空间复杂度：由于递归深度为$logN$，因此空间复杂度为$O(logN)$​

### 5.特殊函数

#### Go实现

``` go
func getLeastNumbers(arr []int, k int) []int {
    sort.Slice(arr, func(i,j int) bool {
        if arr[i] < arr[j] {
            return true
        }
        return false
    })

    return arr[:k]
}
```

* 时间复杂度: $O(nlog(n))$

