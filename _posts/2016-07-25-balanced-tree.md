---
title: Leetcode 110. Balanced Binary Tree - 判断是否为平衡二叉树（递归解法）
layout: post
category : 算法
tagline: "Supporting tagline"
tags : [leetcode , 算法 , swift]
---

## 题目名称

Balanced Binary Tree （判断是否为平衡二叉树）

## 题目地址

[https://leetcode.com/problems/balanced-binary-tree/](https://leetcode.com/problems/balanced-binary-tree/)

## 题目描述
英文：Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.

中文：给一个二叉树，判断是否为平衡二叉树。

## 解法

算法代码（swift）如下：

    class Solution {
	    
	    func getHeight(root: TreeNode?) -> Int{
	        if root == nil {
	            return 0
	        }
	        var leftCount = 0;
	        var rightCount = 0;
	        if root!.left != nil{
	            leftCount = getHeight(root!.left) + 1
	        }
	        
	        if root!.right != nil{
	            rightCount = getHeight(root!.right) + 1
	        }
	        
	        return leftCount > rightCount ? leftCount : rightCount
	        
	    }
	    
	    
	    func isBalanced(root: TreeNode?) -> Bool {
	        if root == nil {
	            return true
	        }
	        var leftCount = root!.left == nil ? 0:getHeight(root!.left)+1
	        var rightCount = root!.right == nil ? 0:getHeight(root!.right)+1
	        if abs(leftCount-rightCount) > 1{
	            return false
	        }else if !isBalanced(root!.left) || !isBalanced(root!.right){
	            return false
	        }else{
	            return true
	        }
	        
	    }
	}

测试用例示例：

![平衡二叉树测试用例](http://spetacular.github.io/images/2016/balanced_tree_test_case.jpg)


	public class TreeNode {
	     public var val: Int
	     public var left: TreeNode?
	     public var right: TreeNode?
	     public init(_ val: Int) {
	         self.val = val
	         self.left = nil
	         self.right = nil
	     }
	}

	//[1,null,2,null,3]
	var r1 = TreeNode(1)
	var r2 = TreeNode(2)
	var r3 = TreeNode(3)
	r1.right = r2
	r2.right = r3
	var s = Solution()
	s.isBalanced(r1)//false





