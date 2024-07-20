---
title: 2269. 找到一个数字的 K 美丽值
date: 2024-07-20 15:36:03
categories:
  - 算法
tags:
  - 算法
  - 定长滑动窗口专题
---

一个整数 `num` 的 **k** 美丽值定义为 `num` 中符合以下条件的 **子字符串** 数目：

- 子字符串长度为 `k` 。
- 子字符串能整除 `num` 。

给你整数 `num` 和 `k` ，请你返回 `num` 的 k 美丽值。

注意：

- 允许有 **前缀** **0** 。
- `0` 不能整除任何值。

一个 **子字符串** 是一个字符串里的连续一段字符序列。

 

**示例 1：**

```
输入：num = 240, k = 2
输出：2
解释：以下是 num 里长度为 k 的子字符串：
- "240" 中的 "24" ：24 能整除 240 。
- "240" 中的 "40" ：40 能整除 240 。
所以，k 美丽值为 2 。
```

**示例 2：**

```
输入：num = 430043, k = 2
输出：2
解释：以下是 num 里长度为 k 的子字符串：
- "430043" 中的 "43" ：43 能整除 430043 。
- "430043" 中的 "30" ：30 不能整除 430043 。
- "430043" 中的 "00" ：0 不能整除 430043 。
- "430043" 中的 "04" ：4 不能整除 430043 。
- "430043" 中的 "43" ：43 能整除 430043 。
所以，k 美丽值为 2 。
```

 

**提示：**

- `1 <= num <= 109`
- `1 <= k <= num.length` （将 `num` 视为字符串）

解法1：暴力破解：

```java
class Solution {
    public int divisorSubstrings(int num, int k) {
        char[] array = String.valueOf(num).toCharArray();
        int result = 0;
        for (int i = 0; i < array.length; i++) {
            if (i + k <= array.length) {
                // 判断是否可以整除
                int kSub = getShortK(array, i, i + k - 1);
                System.out.println("kSub = " + kSub);
                if (kSub != 0 && num % kSub == 0) {
                    result++;
                }
            }
        }
        return result;
    }

    private int getShortK(char[] array, int start, int end) {
        StringBuilder builder = new StringBuilder();
        for (int i = start; i <= end; i++) {
            builder.append(array[i]);
        }
        return Integer.valueOf(builder.toString());
    }
}
```



其他人：

```java
class Solution {
    public int divisorSubstrings(int num, int k) {
        String value = String.valueOf(num);
        
        int left = 0; //左侧索引

        int right = k-1; //右侧索引

        int temp = 0; // 被整除的值

        int result = 0; // 美丽值结果

        while(right < value.length()){
            for(int i = left;i< right+1; i++){
                int t = value.charAt(i) - '0';
                temp += t * Math.pow(10,right-i);
            }
            if(temp != 0 && num % temp == 0){
                result++;
            }
            //被整除的值归0
            temp = 0;
            //指针同时移动
            right++;
            left++;
        }
        return result;
    }

    
}
```

