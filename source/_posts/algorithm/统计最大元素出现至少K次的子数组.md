---
title: 2962.统计最大元素出现至少K次的子数组
date: 2024-10-15 23:31:57
categories:
  - 算法
tags:
  - 算法
  - 不定长滑动窗口专题
  - 求子数组个数
---

给你一个整数数组 `nums` 和一个 **正整数** `k` 。

请你统计有多少满足 「 `nums` 中的 **最大** 元素」至少出现 `k` 次的子数组，并返回满足这一条件的子数组的数目。

子数组是数组中的一个连续元素序列。

 

**示例 1：**

```
输入：nums = [1,3,2,3,3], k = 2
输出：6
解释：包含元素 3 至少 2 次的子数组为：[1,3,2,3]、[1,3,2,3,3]、[3,2,3]、[3,2,3,3]、[2,3,3] 和 [3,3] 。
```

**示例 2：**

```
输入：nums = [1,4,2,1], k = 3
输出：0
解释：没有子数组包含元素 4 至少 3 次。
```

 

**提示：**

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^6`
- `1 <= k <= 10^5`



```java
class Solution {
    public long countSubarrays(int[] nums, int k) {
        // 找到最大元素
        int max = 0;

        int n = nums.length;

        for (int i = 0; i < n; i++) {
            max = Math.max(max, nums[i]);
        }

        int maxCount = 0;
        int left = 0;
        long result = 0;
        for (int right = 0; right < n; right++) {
            if (nums[right] == max) {
                maxCount++;
            }
            while (maxCount == k) {
                if (nums[left] == max) {
                    maxCount--;
                }
                left++;
            }
            result += left;
        }
        return result;

    }
}
```

