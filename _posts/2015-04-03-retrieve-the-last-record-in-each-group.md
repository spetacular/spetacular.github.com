---
layout: post
title: 检索每个分组的最后一条记录
---


有这样一类问题：


- 检索论坛中某一版块所有主题的最新一条帖子

- 查找所有会话中最新一条消息

- 查找一类商品的最新报价

这类问题的共同点是：需要按某个字段分组，且每组只能取一条记录；按某个字段倒序。

举例来说，有这样一个表：

    mysql> select * from t_tmp;
	+-----+---------+---------------------+
	| FId | FCityId | FUpdateTime         |
	+-----+---------+---------------------+
	|   1 |       1 | 2014-10-10 00:00:00 |
	|   2 |       1 | 2014-10-11 00:00:00 |
	|   3 |       2 | 2014-10-10 10:00:00 |
	+-----+---------+---------------------+

表1



希望从中找出每个FCityId的最新更新记录，即筛选出这样的结果：
    
	+-----+---------+---------------------+
	| FId | FCityId | FUpdateTime         |
	+-----+---------+---------------------+
	|   2 |       1 | 2014-10-11 00:00:00 |
	|   3 |       2 | 2014-10-10 10:00:00 |
	+-----+---------+---------------------+

表2

# Group By #
Po主最开始的方法是用group by（So Easy！）

	select * from t_tmp group by FCityId order by FUpdateTime desc;`
结果却是错误的！

    +-----+---------+---------------------+
	| FId | FCityId | FUpdateTime         |
	+-----+---------+---------------------+
	|   3 |       2 | 2014-10-10 10:00:00 |
	|   1 |       1 | 2014-10-10 00:00:00 |
	+-----+---------+---------------------+

表3


其原因是：Group By 要先于 Order By执行。Group By执行分组之后，记录中只剩下1和3了：

    mysql> SELECT * FROM t_tmp GROUP BY FCityId LIMIT 0 , 30;
	+-----+---------+---------------------+
	| FId | FCityId | FUpdateTime         |
	+-----+---------+---------------------+
	|   1 |       1 | 2014-10-10 00:00:00 |
	|   3 |       2 | 2014-10-10 10:00:00 |
	+-----+---------+---------------------+

表4


然后执行Order By，就变成了图3的结果。

> TIPS：执行顺序 Where > Group By > Order By

# SUB SELECT #
很自然的一个解决方法就是：既然Group By先于Order By改变结果，那么就在Group By之前纠正结果。方法是子查询。
如表4，Group By从头到尾扫一遍，留下了第1和第3两条记录。如果从尾到头扫一遍，就留下3和2两条记录。然后执行Order By，就能得到期望的结果。

    mysql> SELECT * FROM (SELECT * FROM t_tmp ORDER BY FUpdateTime DESC)tmptable GROUP BY FCityId ORDER BY FUpdateTime DESC;
	+-----+---------+---------------------+
	| FId | FCityId | FUpdateTime         |
	+-----+---------+---------------------+
	|   2 |       1 | 2014-10-11 00:00:00 |
	|   3 |       2 | 2014-10-10 10:00:00 |
	+-----+---------+---------------------+

表5
	

但子查询执行效率不高，容易形成慢SQL。


# LEFT JOIN #
再分析整个问题，其中蕴含着组内FId从大到小排列的条件，这种表内字段的自我比较，可以用JOIN命令来做。

如图：

![Mysql Join](http://spetacular.github.io/images/2015-04-03/mysql_join.png)

我们可以用LEFT JOIN去掉部分记录（即FId不按从大到小排列的记录）：

    mysql> SELECT m1. * FROM t_tmp m1 LEFT JOIN t_tmp m2 ON ( m1.FCityId = m2.FCity
	Id AND m1.FId < m2.FId )  WHERE m2.FId IS NULL ORDER BY FUpdateTime DESC LIMIT 0
	, 30;
	+-----+---------+---------------------+
	| FId | FCityId | FUpdateTime         |
	+-----+---------+---------------------+
	|   2 |       1 | 2014-10-11 00:00:00 |
	|   3 |       2 | 2014-10-10 10:00:00 |
	+-----+---------+---------------------+

表6

# 通用方法 #
经过以上的讨论，可以形成此类问题的较为通用的方法。

直观但低效的方法：

	`select * from (select * from messages ORDER BY id DESC) AS x GROUP BY name

难懂但高效的方法：

	`SELECT m1.*
	FROM messages m1 LEFT JOIN messages m2
 	ON (m1.name = m2.name AND m1.id < m2.id)
	WHERE m2.id IS NULL;

