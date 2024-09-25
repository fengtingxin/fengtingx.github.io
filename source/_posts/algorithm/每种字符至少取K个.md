---
title: 2516.每种字符至少取K个
date: 2024-09-24 22:39:07
categories:
  - 算法
tags:
  - 算法
  - 不定长滑动窗口专题
---

给你一个由字符 `'a'`、`'b'`、`'c'` 组成的字符串 `s` 和一个非负整数 `k` 。每分钟，你可以选择取走 `s` **最左侧** 还是 **最右侧** 的那个字符。

你必须取走每种字符 **至少** `k` 个，返回需要的 **最少** 分钟数；如果无法取到，则返回 `-1` 。

 

**示例 1：**

```
输入：s = "aabaaaacaabc", k = 2
输出：8
解释：
从 s 的左侧取三个字符，现在共取到两个字符 'a' 、一个字符 'b' 。
从 s 的右侧取五个字符，现在共取到四个字符 'a' 、两个字符 'b' 和两个字符 'c' 。
共需要 3 + 5 = 8 分钟。
可以证明需要的最少分钟数是 8 。
```

**示例 2：**

```
输入：s = "a", k = 1
输出：-1
解释：无法取到一个字符 'b' 或者 'c'，所以返回 -1 。
```

 

**提示：**

- `1 <= s.length <= 10^5`
- `s` 仅由字母 `'a'`、`'b'`、`'c'` 组成
- `0 <= k <= s.length`

```java
class Solution {
    public int takeCharacters(String s, int k) {
        
        //求中间子数组的最大长度.
        //你必须取走每种字符 至少 k 个
        int count[] = new int[3];
        //检查每个字符是否只有3个.
        for(int i = 0; i < s.length() ; i++){
            count[s.charAt(i) - 'a'] ++;
        }

        //check count[] 每个元素是否都小于k
        for(int i = 0; i < count.length ; i++){
            if(count[i] < k){
                return -1;
            }
        }

        //最重要的开始的了，left/right
        //想法：
        int sum[] = new int[3]; //记录子数组的个数.,
        //每次都往这个数组中放，当发现这个数组中，数字超出 总数- k时，则l向后移动，发现还是超出，则继续移动.一直到不超出.
        //计算r - l + 1的长度.

        int result = 0;
        int left = 0;
        for(int right = 0; right < s.length() ; right++){
            sum[s.charAt(right) - 'a'] ++;
            while(!checkSumIsRight(sum,count,k)){
                sum[s.charAt(left) - 'a'] --;
                left++;
            }
            result = Math.max(result,right - left + 1);
        }
        return s.length() - result;
    }

    //检查数组中数据是否已经符合预期
    private boolean checkSumIsRight(int[] sum, int[] count, int k){
        for(int i = 0; i < count.length ; i++){
            if(sum[i] > count[i] - k){
                return false;
            }
        }
        return true;
    }
}
```

