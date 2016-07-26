---
title: Leetcode 21. Merge Two Sorted Lists - 合并有序链表（递归与非递归解法）
layout: post
category : 算法
tagline: "Supporting tagline"
tags : [leetcode , 算法 , swift]
---

## 题目名称

Merge Two Sorted Lists （合并有序链表）

## 题目地址

[https://leetcode.com/problems/merge-two-sorted-lists/](https://leetcode.com/problems/merge-two-sorted-lists/)

## 题目描述
英文：Merge two sorted linked lists and return it as a new list. The new list should be made by splicing together the nodes of the first two lists.

中文：有两个有序链表，将之合并为一个有序链表。

## 解法1 递归
合并过程是这样的：先比较两个链表的头节点，节点值小的先摘到新链表中；然后再处理剩下的链表，直到两个链表都为空。递归解法比较容易实现。

算法代码（swift）如下：

    class Solution {
	    func mergeTwoLists(l1: ListNode?, _ l2: ListNode?) -> ListNode? {
	        if l1 == nil{
	            return l2
	        }
	        if l2 == nil{
	            return l1
	        }
	        var ret: ListNode?
	        if l1!.val > l2!.val{
	            ret = l2
	            ret!.next = mergeTwoLists(l1,l2!.next)
	        } else {
	            ret = l1
	            ret!.next = mergeTwoLists(l1!.next,l2)
	        }
	        return ret
	    }
	}

## 解法2 非递归
也可以用while循环来做。就是用一个tail指针遍历两个链表，每次走到值较小的节点上，直到其中一个链表走到头。最后拼上另一个链表即可。

算法代码（swift）如下：

	
	class Solution {
	    func mergeTwoLists(l1: ListNode?, _ l2: ListNode?) -> ListNode? {
	        if l1 == nil{
	            return l2
	        }
	        if l2 == nil{
	            return l1
	        }
	        var head: ListNode?
	        var tail: ListNode?
	        var ll1 = l1
	        var ll2 = l2
	        if l1!.val > l2!.val{
	            head = l2
	            ll2 = l2!.next
	        }else{
	            head = l1
	            ll1 = l1!.next
	        }
	        tail = head
	        head?.next = tail
	        while ll1 != nil && ll2 != nil{
	            if ll1!.val > ll2!.val{
	                tail?.next = ll2
	                tail = ll2
	                ll2 = ll2?.next
	            }else{
	                tail?.next = ll1
	                tail = ll1
	                ll1 = ll1?.next
	            }
	        }
	        if ll1 == nil{
	            tail?.next = ll2
	        }
	        if ll2 == nil{
	            tail?.next = ll1
	        }
	        return head
	    }
	}


假设l1长度为M，l2长度为N，则算法复杂度为O(M+N)。





