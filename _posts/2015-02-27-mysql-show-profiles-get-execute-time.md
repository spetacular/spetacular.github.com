---
layout: post
title: MySQL显示执行SQL耗时，精确到毫秒
---
MySQL执行一个SQL语句时，执行时间精确到秒。如下：

    mysql> select * from test
	+----+-------+
	| id | name  |
	+----+-------+
	|  1 | david |
	+----+-------+
	1 row in set (0.00 sec)

如何精确到毫秒呢？MySQL有个内置语句[(show profile)](http://dev.mysql.com/doc/refman/5.5/en/show-profile.html "(show profile)")可以查看执行耗时。

    mysql> set profiling=1;
	Query OK, 0 rows affected (0.00 sec)

然后执行查询语句。
使用show profiles就可以看到执行时间了。

    mysql> show profiles;
	+----------+------------+--------------------+
	| Query_ID | Duration   | Query              |
	+----------+------------+--------------------+
	|        1 | 0.00034500 | select * from test |
	+----------+------------+--------------------+
	1 row in set (0.04 sec)
Duration就是该SQL的执行时间，将其乘以1000就是毫秒数了。