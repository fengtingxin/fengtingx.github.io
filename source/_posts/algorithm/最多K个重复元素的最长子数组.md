---
title: 2958. 最多 K 个重复元素的最长子数组
date: 2024-09-19 23:14:05
categories:
  - 算法
tags:
  - 算法
  - 不定长滑动窗口专题
---

给你一个整数数组 `nums` 和一个整数 `k` 。

一个元素 `x` 在数组中的 **频率** 指的是它在数组中的出现次数。

如果一个数组中所有元素的频率都 **小于等于** `k` ，那么我们称这个数组是 **好** 数组。

请你返回 `nums` 中 **最长好** 子数组的长度。

**子数组** 指的是一个数组中一段连续非空的元素序列。

 

**示例 1：**

```
输入：nums = [1,2,3,1,2,3,1,2], k = 2
输出：6
解释：最长好子数组是 [1,2,3,1,2,3] ，值 1 ，2 和 3 在子数组中的频率都没有超过 k = 2 。[2,3,1,2,3,1] 和 [3,1,2,3,1,2] 也是好子数组。
最长好子数组的长度为 6 。
```

**示例 2：**

```
输入：nums = [1,2,1,2,1,2,1,2], k = 1
输出：2
解释：最长好子数组是 [1,2] ，值 1 和 2 在子数组中的频率都没有超过 k = 1 。[2,1] 也是好子数组。
最长好子数组的长度为 2 。
```

**示例 3：**

```
输入：nums = [5,5,5,5,5,5,5], k = 4
输出：4
解释：最长好子数组是 [5,5,5,5] ，值 5 在子数组中的频率没有超过 k = 4 。
最长好子数组的长度为 4 。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^9`
- `1 <= k <= nums.length`

```java
class Solution {
    public int maxSubarrayLength(int[] nums, int k) {
        //使用两个指针和一个map结构，map接口用于存储左右指针之间的所有数值的重复次数
        //当发现下一个添加到map结构中时，value大于k ,则，尝试将左指针向右移动.否则将右指针向右移动.

        int left = 0;
        int right = 0;
        Map<Integer,Integer> map = new HashMap<>();

        int result = 0;

        for(; right < nums.length ; right++){
            //添加到计数器中
            map.put(nums[right],map.getOrDefault(nums[right],0)+1);

            while(map.get(nums[right]) > k){
            
                map.put(nums[left],map.getOrDefault(nums[left],0)-1);
                
                left++;
            }
            result = Math.max(right-left+1,result);
        }
        return result;
    }
}
```

