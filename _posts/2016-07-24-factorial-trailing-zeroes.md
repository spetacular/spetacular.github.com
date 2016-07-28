---
title: Leetcode 172. Factorial Trailing Zeroes - 阶乘后缀0的数目（O(N)与O(logN)解法）
layout: post
category : 算法
tagline: "Supporting tagline"
tags : [leetcode , 算法 , swift]
---

## 题目名称

Factorial Trailing Zeroes （阶乘后缀0的数目）

## 题目地址

[https://leetcode.com/problems/factorial-trailing-zeroes/](https://leetcode.com/problems/factorial-trailing-zeroes/)

## 题目描述
英文：Given an integer n, return the number of trailing zeroes in n!.   
Note: Your solution should be in logarithmic time complexity.

中文：给一个整型数字n，返回n!后缀0的数目。需要对数级别的时间复杂度。

例如：10! = 3628800，后缀有两个0，返回2。

## 解法1 O(N) 
首先是不太可能直接计算n!，然后看后缀0的数目，因为n!太大了！  
我们先用笨办法，先算出前25个数字的结果，看能否找到规律。  
![n!与后缀0数目的关系](http://spetacular.github.io/images/2016/zeros.png)
可以看到，后缀0数目发生变化的点，都是5的整数倍，如5，10，15，20，25...
这容易理解，产生后缀0，只能是与10相乘，而10 ＝ 2 * 5，2广泛存在于偶数中，所以不用关注。只需要知道5的倍数的整数即可。

给定整数n，要找到以下数字，其中x*5^y <= n：

    5,10,15,...,x*5^y

然后看每个数字能分解出x个5:

    1,1,1,...,y

最后的结果就是如下：

    Sum(1,1,1,...,y)

算法代码（swift）如下：

    class Solution {
	    func trailingZeroes(n: Int) -> Int {
	        if n < 5{
	            return 0
	        }
	        var i = 5
	        var ret = 0
	        while i <= n{
	            ret += num_of_five(i)
	            i += 5
	        }
	        return ret
	    }
	    
	    func num_of_five(n: Int) ->(Int) {
	        var i = n
	        var ret = 0
	      
	        while i > 0{
	            if i % 5 != 0 {
	                break
	            }
	            i = i / 5
	            if i >= 1{
	                ret++
	            }
	        }
	        
	        return ret
	    }
	}

这个算法需要循环n/5次，时间复杂度为O(N)，尽管“政治正确”，但不是最好的解法。

## 解法2 O(logN)

将解法1的过程再回顾下，计算5出现的次数，可以分解看：

	出现5，计数加1；
	出现5*5，计数加2；
	出现5*5*5，计数加3；

换个角度看，

	除以5，计数加1；
	除以5，计数加1；除以5*5，计数加1；
	除以5，计数加1；除以5*5，计数加1；除以5*5*5，计数加1；


那么最后结果

	floor(n/5) + floor(n/25) + floor(n/125) + ....

算法代码（swift）如下：

	class Solution1 {
	    func trailingZeroes(n: Int) -> Int {
	        var i = 5
	        var ret = 0
	        while i <= n{
	            ret += n / i
	            i *= 5
	        }
	        return ret
	    }
	}	


算法复杂度为O(logN)。





