# 剑指 Offer 58 - I. 翻转单词顺序

## 题目描述

这是LeetCode上的[翻转单词顺序](https://leetcode-cn.com/problems/fan-zhuan-dan-ci-shun-xu-lcof/), 难度为简单.

输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。例如输入字符串"I am a student. "，则输出"student. a am I"。

### 实例1

```
输入: "the sky is blue"
输出: "blue is sky the"
```

### 实例2

```
输入: "  hello world!  "
输出: "world! hello"
解释: 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
```

### 实例3

```
输入: "a good   example"
输出: "example good a"
解释: 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。
```

### 说明

* 无空格字符构成一个单词。

* 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。

* 如果两个单词间有多余的空格，将反转后单词间的空格减少到只含一个。

***

## 解答

### 1.手动split

用start,end来代表单个substring的开始和结束，prevStart和prevEnd来表示前一个substring的开始和结束。

遍历给定字符串，如果位于字符串第一个字符处且第一个字符不为`" "`，或当前字符不为`" "`且当前字符前的一个字符为`" "`时，更新startde

#### Java

```Java
class Solution {
    public String reverseWords(String s) {
        int start = -1;
        int end = -1;
        int prevStart = -1;
        int prevEnd = -1;
        ArrayList<String> strings = new ArrayList();
        String toReturn = "";
        for(int i = 0; i < s.length(); i++){
            if((i == 0 && s.charAt(0) != ' ') ||
            (s.charAt(i) != ' ' && s.charAt(i-1) == ' ')){
                start = i;
            }
            if(i != 0 && s.charAt(i) == ' ' && s.charAt(i-1) != ' '){
                end = i;
            }
            if(i == s.length() - 1 && s.charAt(i) != ' '){
                end = i + 1;
            }
            if(start != prevStart && end != prevEnd){
                strings.add(s.substring(start, end));
                prevStart = start;
                prevEnd = end;
            }
        }
        for(int i = strings.size() - 1; i > 0; i--){
            toReturn += strings.get(i) + " ";
        }
        if(strings.size() != 0){
            toReturn += strings.get(0);
        }
        return toReturn;
    }
}
```

#### Go

``` go
func reverseWords(s string) string {
    var return_string string
    m := make(map[int]string)
    substring := []rune{} 
    count := 0
    for _,j := range s {
        if string(j) != " " {
            substring = append(substring,j)
            //fmt.Println(string(substring))
            continue
        } else {
            if string(substring) == " "{
                continue
            } else {
                m[count] = string(substring)
                count++
                substring = []rune{}
            }
            //fmt.Println(m[count])
            
            
        }
    }
    //if string(substring) != " " {
    m[count] = string(substring)
        //substring = []rune{}
    //}
    m[count] = string(substring)
    for i:=len(m)-1;i>=0;i--{
        if m[i] != ""{
            return_string = return_string + m[i]
            return_string = return_string + " "
        }
        
    }
    if len(return_string) > 1 {
        return_string = return_string[:len(return_string)-1]
        return return_string
    } else {
        return ""
    }
    
    return return_string
}
```

### 2.双指针

#### Go

```go
func reverseWords(ss string) string {
    n:=len(ss)
    l,r := 0, n-1
    // [0, n-1]
    s:=[]byte(ss)
    // 处理两边空格二
    for l<n && s[l] == ' ' {l++}
    for r>=0 && s[r] == ' ' {r--}
    // 起始位置(可能变化)
    st:=l
    for i:=l; i<=r;i ++{
        if s[i] == ' ' {
            reverse(s, st, i) // s is what we want to reverse, st is the start point(included), i is not included
            st = i+1
        }
        if i == r {
            reverse(s,st,r+1)   
        }
    }
    reverse(s,l,r+1)
    // 2.双指针处理 多余的空格
    // [1,r]
    // 双指针初始化为左边界
    sl, sr := l,l
    for sr <= r {
        // 对于多于1的空格，按一个处理
        f:=0
        for sr <= r && s[sr] == ' ' {sr++; f = 1}
        if f == 1 {sr--}
        s[sl] = s[sr]
        sl++
        sr++
    }
    // 最后答案[1,sr)
    return string(s[l:sl])
}
// [l,r)
func reverse(s []byte,l,r int) {
    r--
    for l < r {
        s[l], s[r] = s[r], s[l]
        l++
        r--
    }
}
```

