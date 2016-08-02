---
title: Leetcode 344. Reverse String - 反转字符串
layout: post
category : 算法
tagline: "Supporting tagline"
tags : [leetcode , 算法 , swift]
---

## 题目名称

Reverse String （反转字符串）

## 题目地址

[https://leetcode.com/problems/reverse-string/](https://leetcode.com/problems/reverse-string/)

## 题目描述
英文：Write a function that takes a string as input and returns the string reversed.

Example:
Given s = "hello", return "olleh".

中文：反转一个字符串。例如"hello"，转化为"olleh"。

## 解法

算法代码（swift）如下：

    class Solution {
	    func reverseString(s: String) -> String {
	        var len = s.characters.count
	        var sArr  = [Character](count:len, repeatedValue:" ")
	        for i in s.characters{
	            sArr[--len] = i
	        }
	        
	        return String(sArr)
	    }
	}

算法复杂度为O(N)。

还有一种时间复杂度为O(N/2)的解法，即交换与中心对称位置的字符，这样可以只循环N/2次。



