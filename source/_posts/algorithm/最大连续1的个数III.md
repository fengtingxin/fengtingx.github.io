---
title: 1004.最大连续1的个数III
date: 2024-09-20 22:24:10
categories:
  - 算法
tags:
  - 算法
  - 不定长滑动窗口专题
---

给定一个二进制数组 `nums` 和一个整数 `k`，如果可以翻转最多 `k` 个 `0` ，则返回 *数组中连续 `1` 的最大个数* 。

 

**示例 1：**

```
输入：nums = [1,1,1,0,0,0,1,1,1,1,0], K = 2
输出：6
解释：[1,1,1,0,0,1,1,1,1,1,1]
粗体数字从 0 翻转到 1，最长的子数组长度为 6。
```

**示例 2：**

```
输入：nums = [0,0,1,1,0,0,1,1,1,0,1,1,0,0,0,1,1,1,1], K = 3
输出：10
解释：[0,0,1,1,1,1,1,1,1,1,1,1,0,0,0,1,1,1,1]
粗体数字从 0 翻转到 1，最长的子数组长度为 10。
```

**提示：**

- `1 <= nums.length <= 10^5`
- `nums[i]` 不是 `0` 就是 `1`
- `0 <= k <= nums.length`

```java
class Solution {
    public int longestOnes(int[] nums, int k) {

        //使用双指针; 右侧指针先行，记录1的个数.
        int l = 0;

        int result = 0;

        int temp = 0;
    
        for(int r = 0; r < nums.length ; r++){
            temp += nums[r];
            while((r-l+1) - temp > k){
                temp = temp - nums[l];
                l++;
            }
            result = Math.max(result,r-l+1);
        }
        return result;
    }
}
```

