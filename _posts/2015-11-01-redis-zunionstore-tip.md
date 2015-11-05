---
title: Redis技巧:Sorted Set使用
layout: post
category : redis
tagline: "Supporting tagline"
tags : [redis]
---

有序集合(Sorted Set)是Redis一个很重要的数据结构，它用来保存需要排序的数据。例如排行榜，一个班的语文成绩，一个公司的员工工资，一个论坛的帖子等。有序集合中，每个元素都带有score（权重），以此来对元素进行排序。它有三个元素：key、member和score。以语文成绩为例，key是考试名称（期中考试、期末考试等），member是学生名字，score是成绩。

有序集合有两大基本用途：排序和聚合，以下用几个例子分别说明。

# 排序 
假设老师需要处理期中考试的语文成绩，他做的第一件事是将学生成绩录入系统。

      Li Lei成绩70分
      127.0.0.1:6379> ZADD mid_test 70 "Li Lei"
      (integer) 1

      Han Meimei成绩70分
      127.0.0.1:6379> ZADD mid_test 70 "Han Meimei"
      (integer) 1

      tom成绩99.5分
      127.0.0.1:6379> ZADD mid_test 99.5 "Tom"
      (integer) 1

###排行榜
有序集合天然就是做排行榜的利器。只需将带score的member塞到有序集合里，就可以正序或倒序取出数据。这要用到ZREVRANGE（倒序）和ZRANGE（正序）。     

      分数排行榜
      127.0.0.1:6379> ZREVRANGE mid_test 0 -1 WITHSCORES
      1) "Tom"
      2) "99.5"
      3) "Li Lei"
      4) "70"
      5) "Han Meimei"
      6) "70"

###分段统计
有序集合还支持按score区间来查询：ZREVRANGEBYSCORE为倒序查询，ZRANGEBYSCORE为正序。例如要知道90分以上的学霸：

      127.0.0.1:6379> ZREVRANGEBYSCORE mid_test 100 90 WITHSCORES
      1) "Tom"
      2) "99.5"


#聚合 
有序集合，其本质是集合，当然会有交集（[ZINTERSTORE](http://redisdoc.com/sorted_set/zinterstore.html "ZINTERSTORE")）和并集（[ZUNIONSTORE](http://redisdoc.com/sorted_set/zunionstore.html "ZUNIONSTORE")）运算。

<img src="http://spetacular.github.io/images/2015-11-01/inter-union.jpg" alt="交集和并集" width="100%"/>

###交集
[ZINTERSTORE](http://redisdoc.com/sorted_set/zinterstore.html "ZINTERSTORE")取所有集合的并集。以两个集合A和B为例，要取交集C，是这样的逻辑：

* A和B中共有的member，会加入到C中，其score等于A、B中score之和。
* 不同时在A和B的member，不会加到C中。

某班又进行了期末考试，同时来了个新同学Jerry。

      127.0.0.1:6379> ZADD fin_test 88 "Li Lei"
      (integer) 1
      127.0.0.1:6379> ZADD fin_test 75 "Han Meimei"
      (integer) 1
      127.0.0.1:6379> ZADD fin_test 99.5 "Tom"
      (integer) 1
      127.0.0.1:6379> ZADD fin_test 100 "Jerry"
      (integer) 1

老师要按期中考试和期末考试的总成绩来排座位，就对mid_test和fin_test做了个交集。

      127.0.0.1:6379> ZINTERSTORE sum_point 2 mid_test fin_test
      (integer) 3
      127.0.0.1:6379> ZREVRANGE sum_point 0 -1 WITHSCORES
      1) "Tom"
      2) "199"
      3) "Li Lei"
      4) "158"
      5) "Han Meimei"
      6) "145"

结果显示了学生的总成绩。
但结果中没有新来的Jerry同学（尽管TA考了100分）。这是坑一。

###并集
[ZUNIONSTORE](http://redisdoc.com/sorted_set/zunionstore.html "ZUNIONSTORE")计算所有集合的并集。以两个集合A和B为例，要取并集C，是这样的逻辑：

* A的所有member会加到C中，其score与A中相等
* B的所有member会加到C中，其score与B中相等
* A和B中共有的member，其score等于A、B中score之和。

假设某公司要核算工资总支出，先由各部门独自核算，再由财务统一处理。

程序员工资

      127.0.0.1:6379> zadd programmer 2000 peter
      (integer) 1
      127.0.0.1:6379> zadd programmer 3500 jack
      (integer) 1
      127.0.0.1:6379> zadd programmer 5000 tom
      (integer) 1

经理工资

      127.0.0.1:6379> zadd manager 2000 herry
      (integer) 1
      127.0.0.1:6379> zadd manager 3500 mary
      (integer) 1
      127.0.0.1:6379> zadd manager 4000 tom
      (integer) 1

财务统一处理。

      127.0.0.1:6379> zunionstore salary 2 programmer manager
      (integer) 5
      127.0.0.1:6379> zrange salary 0 -1 withscores
       1) "herry"
       2) "2000"
       3) "peter"
       4) "2000"
       5) "jack"
       6) "3500"
       7) "mary"
       8) "3500"
       9) "tom"
      10) "9000"

结果显示了总工资支出情况。

但结果中程序员tom和经理tom是两个人，但工资算在了一起。这是坑二。

#避免踩坑

还记得上面说的坑一和坑二吗？

坑一：

当进行ZINTERSTORE操作时，如果进行聚合操作的源集合中元素不同，则聚合后的结果集仅为并集。如果发现聚合后少了一些元素，请查看源集合元素是否相同。

坑二：

当进行ZUNIONSTORE操作时，如果进行聚合操作的源集合中有相同元素，则聚合后的结果集中，相同元素的score等于源集合元素的score之和。如果发现聚合后某些元素的score异常，请查看源集合是否有相同元素。

我踩过的坑：

做用户的feed（timeline）时，需要将我关注的人和我自己发表的信息聚合起来。

<img src="http://spetacular.github.io/images/2015-11-01/feed-timeline.jpg" alt="timeline & feed" width="100%"/>

应该用ZUNIONSTORE将所有信息聚合到一起。

后来有用户反馈说timeline排序错误，自己发表发布的信息永远在最上面。后来查明原因，由于早期的bug，自己竟然可以关注自己，导致关注人和自己重复聚合。踩到了坑二。

#为什么踩坑
以坑二为例，为什么有相同元素时，score就会变成原来元素的和？

因为ZINTERSTORE和ZUNIONSTORE有个参数为AGGREGATE，表示结果集的聚合方式，可取SUM、MIN、MAX其中之一。默认值为SUM。

所以不指定聚合方式时，缺省值为SUM，即求和。

<blockquote>
  默认使用的参数 SUM ，可以将所有集合中某个成员的 score 值之 和 作为结果集中该成员的 score 值；使用参数 MIN ，可以将所有集合中某个成员的 最小 score 值作为结果集中该成员的 score 值；而参数 MAX 则是将所有集合中某个成员的 最大 score 值作为结果集中该成员的 score 值。
</blockquote>

文档如上。

#有序集合之总结

使用场景：排行榜，有序列表，聚合；

算法复杂度：

* 增删：O(M*log(N))， N 为有序集的基数， M 为被成功操作（新增、移除）的成员的数量。
* 查询：O(log(N)+M)， N 为有序集的基数，而 M 为结果集的基数。
* 聚合：O(N)+O(M log(M))， N 为给定有序集基数的总和， M 为结果集的基数。
* 总数：O(1)

注意事项：

* ZINTERSTORE操作时,如果发现聚合后少了一些元素，请查看源集合元素是否相同。
* ZUNIONSTORE操作时,如果发现聚合后某些元素的score异常，请查看源集合是否有相同元素。

