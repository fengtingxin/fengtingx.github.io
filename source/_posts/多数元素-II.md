---
title: 多数元素 II
date: 2023-02-23 22:47:12
tags:
  - 
  - 算法
---

给定一个大小为 n 的整数数组，找出其中所有出现超过 ⌊ n/3 ⌋ 次的元素。

 

示例 1：

输入：nums = [3,2,3]
输出：[3]
示例 2：

输入：nums = [1]
输出：[1]
示例 3：

输入：nums = [1,2]
输出：[1,2]


提示：

1 <= nums.length <= 5 * 104
-109 <= nums[i] <= 109


进阶：尝试设计时间复杂度为 O(n)、空间复杂度为 O(1)的算法解决此问题。

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/majority-element-ii



解法1：使用额外的空间：

```java
class Solution {
    public List<Integer> majorityElement(int[] nums) {
        //解题直接先写map形式：利用空间复杂度解决问题，同时锻炼对map的记忆。
        Map<Integer,Integer> map = new HashMap<>();
        for(int i = 0; i< nums.length;i++){
            map.put(nums[i],map.getOrDefault(nums[i],0)+1);
        }
        int needNum = nums.length / 3;
        //得到map的结果之后那就是找到出现频率最多的其中两个值了
        List<Integer> result = new ArrayList<>();
        for(Map.Entry<Integer,Integer> entry: map.entrySet()){
            if(entry.getValue() > needNum){
                result.add(entry.getKey());
            }
        }
        return result;
    }
}
```



解法2：

非人哉。！！！

```java
class Solution {
    public List<Integer> majorityElement(int[] nums) {

        //分为两种情况：
        if(nums.length <3){
            if(nums.length == 2){
                if(nums[0] != nums[1]){
                    return Arrays.asList(nums[0],nums[1]);
                }
            }
            return Arrays.asList(nums[0]);
        }
        //
        Integer flagA = null; // A 山头
        Integer flagB = null; // B 山头
        Integer countX = 0; //A山头人数
        Integer countY = 0; //B山头人数

        int i = 0;
        //获取山头的数据
        for(;i< nums.length; i++){
            if(flagA != null && flagB != null){
                break;
            }
            if(flagA == null){
                flagA = Integer.valueOf(nums[i]);
                countX++;
            } else if(flagA == nums[i]){
                countX++;
            } else if(flagB == null){
                flagB = Integer.valueOf(nums[i]);
                countY++;
            }
        }

        //假设有两个山头，100人要抢这两个山头,那么肯定有<=2个 队伍的人数>=34人。
        //那么按照拼杀原则的话，最后赢得肯定是这两个队伍的人
        for(; i< nums.length;i++){
            if(flagA == nums[i]){
                countX++;
            } if(countX == 0 && (nums[i] != flagB)){
                flagA = nums[i];
                countX = 1;
            }
            if(flagB == nums[i]){
                countY++;
            } if(countY == 0 && (nums[i] != flagA)){
                flagB = nums[i];
                countY = 1;
            }
            //由于这里会拿非山头的两次攻打山头，会造成山头的人员多死，所以此处标记下。
            if(nums[i] != flagB && nums[i] != flagA){
                countX--;
                countY--;
            }
        }

        //上面循环最终得出的数据为人数最多的<=2只队伍。
        
        int count1 = 0;
        int count2 = 0;
        for( int j = 0; j< nums.length;j++){
            if(flagA != null && nums[j] == flagA){
                count1++;
            }
            if(flagB != null && nums[j] == flagB){
                count2++;
            }
        }
        int needNum = nums.length/3;
        List<Integer> result = new ArrayList<>();
        if(count1 > needNum){
            result.add(flagA);
        }
        if(count2 > needNum){
            result.add(flagB);
        }
        return result;
    }
}
```

