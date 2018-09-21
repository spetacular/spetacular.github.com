---
layout: post
title: 每周学习第 2 期：01背包问题
category : 数学
tagline: "Supporting tagline"
tags : [数学,go,算法,每周学习]
---

# 一、定义
01 背包问题是在资源有限的情况下，实现收益最大化的一类问题。经典的场景是探险家偶然进入一片宝藏，每一件宝贝都是独一无二的，具有自己的价值和重量。由于背包容量有限，只能选择某几样宝贝。此类问题归属于 [动态规划](https://zh.wikipedia.org/zh-hans/%E5%8A%A8%E6%80%81%E8%A7%84%E5%88%92)。

# 二、实现
直观来看，这是一个如何选择的问题。对一个宝贝i来说，是拿还是不拿，这是一个问题。
01背包问题的公式如下：
![01背包问题公式](/images/2018/01package-formula.svg)

选择i的收益是
```
宝贝i的价值
+
为了选择i，背包容量减少为背包容量 - 宝贝i的重量，这时能装的宝贝的价值
```
不选择i的收益是
```
上一次选完宝贝后总的价值。即就当做没看见i。
```

代码实现见 [01packege](
https://github.com/spetacular/weekly-learning/blob/master/ch2/package01.go)
# 三、参考链接
[动态规划：背包问题](https://zhuanlan.zhihu.com/p/25299111)  

[动态规划 之 0-1背包问题](https://blog.csdn.net/crayondeng/article/details/15784093)

[动态规划之01背包问题（最易理解的讲解）](https://blog.csdn.net/mu399/article/details/7722810)