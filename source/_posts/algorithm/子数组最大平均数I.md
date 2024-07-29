---
title: 643.子数组最大平均数I
date: 2024-07-29 22:30:49
categories:
  - 算法
tags:
  - 算法
  - 定长滑动窗口专题
---

给你一个由 `n` 个元素组成的整数数组 `nums` 和一个整数 `k` 。

请你找出平均数最大且 **长度为 `k`** 的连续子数组，并输出该最大平均数。

任何误差小于 `10-5` 的答案都将被视为正确答案。

 

**示例 1：**

```
输入：nums = [1,12,-5,-6,50,3], k = 4
输出：12.75
解释：最大平均数 (12-5-6+50)/4 = 51/4 = 12.75
```

**示例 2：**

```
输入：nums = [5], k = 1
输出：5.00000
```

 

**提示：**

- `n == nums.length`
- `1 <= k <= n <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

```java
class Solution {
    public double findMaxAverage(int[] nums, int k) {
        int sum = 0;
        for(int i = 0; i< k; i++){
            sum += nums[i];
        }
        int resultSum = sum ;
        //得到前k个数的总和
        
        //然后依次向后循环.

        int length = nums.length;
        for(int i = k; i< length;i++){
            sum = sum - nums[i-k] + nums[i];
            resultSum = Math.max(sum,resultSum);
        }

        return 1.0 * resultSum / k;
    }
}
```

