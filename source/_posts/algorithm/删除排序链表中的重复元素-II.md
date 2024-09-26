---
title: 删除排序链表中的重复元素 II
date: 2023-03-17 23:39:58
tags:
  - 算法
  - 链表
---

给定一个已排序的链表的头 head ， 删除原始链表中所有重复数字的节点，只留下不同的数字 。返回 已排序的链表 。

 

示例 1：


输入：head = [1,2,3,3,4,4,5]
输出：[1,2,5]
示例 2：


输入：head = [1,1,1,2,3]
输出：[2,3]


提示：

链表中节点数目在范围 [0, 300] 内
-100 <= Node.val <= 100
题目数据保证链表已经按升序 排列

来源：力扣（LeetCode）
链接：https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii



**链表的题通常需要注意两点：**

1. **舍得用变量，千万别想着节省变量，否则容易被逻辑绕晕**
2. **head 有可能需要改动时，先增加一个 假head，返回的时候直接取 假head.next，这样就不需要为修改 head 增加一大堆逻辑了。**



自己写：

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        //整理一下思路：
        if(head == null || head.next == null){
            return head;
        }

        //先初始化一个结束的节点，期望用结束的节点返回结果
        ListNode result = new ListNode(0, head);
        ListNode current = result;

        //注意范围的内容为result.next
        //链表中删除节点的方式是xx.next = xx.next.next;
        while(current.next != null && current.next.next != null){
            //临时值
            int temp = current.next.val;
            boolean tempB = false;
            //如果当前节点同下一个节点相同，那么将下一个节点删除，挪移到下下个节点
            while(current.next.next != null && temp == current.next.next.val){
                tempB = true;
                current.next = current.next.next;
            }
            if(!tempB){
                current = current.next;
            } else {
                if(temp == current.next.val){
                    current.next = current.next.next;
                }
            }
        }
        return result.next;
    }
}
```





优化版：

为什么优化？增加了一个boolean的类型是否可以去掉？

结论是可以去掉，建议背过。

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        //整理一下思路：
        if(head == null || head.next == null){
            return head;
        }

        //先初始化一个结束的节点，期望用结束的节点返回结果
        ListNode result = new ListNode(0, head);
        ListNode current = result;

        //注意范围的内容为result.next
        //链表中删除节点的方式是xx.next = xx.next.next;
        while(current.next != null && current.next.next != null){
            if(current.next.val == current.next.next.val){
                //如果相同，那么就需要循环判断下面的值是否相同。
                int temp = current.next.val;
                while(current.next != null && temp == current.next.val){
                    current.next = current.next.next;
                }
            } else {
                current = current.next;
            }
        }
        return result.next;
    }
}
```

