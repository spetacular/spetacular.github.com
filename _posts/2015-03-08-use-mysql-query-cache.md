---
layout: post
title: Mysql技巧 -- 合理利用查询缓存优化查询效率
---

最近开发会员中心项目，遇到多表查询的问题，发现响应极慢，就动手查下原因，并进行一些优化。先说下成果吧，由7-8秒降到200ms以下。
吃公鸡下的蛋之前，走道是这样的：
 
![Mysql Join](http://spetacular.github.io/images/2015-03-08/before-optimization.png)

吃完了之后，那家伙，再看，就成了这样：
 
![Mysql Join](http://spetacular.github.io/images/2015-03-08/after-optimization.png)

降到还可以接受的范围了。
# 问题 #
我在review代码和需求时，发现用户列表页访问很慢。该页面根据图3中的查询条件，筛选出符合条件的用户记录，每页显示15条记录，显示如图4所示。
 
![Mysql Join](http://spetacular.github.io/images/2015-03-08/filter-conditions.png)

![Mysql Join](http://spetacular.github.io/images/2015-03-08/query-result.png)

看下数据表的规模：
select count(*) from t_uc_user;
+----------+
| count(*) |
+----------+
|  2001935 | 
+----------+
 200万条记录。
# 定位问题 #
听前辈说，解决查询问题的瓶颈，最重要的是查出瓶颈在哪。我深以为然，所以第一步就是把SQL打印出来。
我发现的可疑点有：
1. 其中好几条SQL都是包含正则表达式的查询，耗时长达2秒以上。标记一下，稍后优化。


    select count(*) as num from t_uc_search_101_user where (((FStatus='1'))) and ((((concat('|',FBusinessInfo, '|') regexp '\\|1_[[:alnum:]]*_[[:alnum:]]*_[[:alnum:]]*_[[:alnum:]]*\\|')))) and ((((concat('|',FAttributeInfo, '|') regexp '\\|1_-1_[[:alnum:]]*\\|'))));
	+-----+
	| num |
	+-----+
	| 3   | 
	+-----+
	1 row in set (2.18 sec)

 2. 有至少三条count(*)查询，看下存储引擎，如果是Innodb，可以考虑用MYISAM。结果是MYISAM，此路不通。
3. 重复执行相同的SQL语句。这些SQL语句的功能是根据查询到的用户记录ID，去取用户的详细信息。以每页15条来看，可以减少15次查询。

    select * from t_uc_records_101 where ( (FId='5878970') OR (FId='5878969') OR (FId='5880317') ) AND FStatus!=-1
	select * from t_uc_records_101 where ( (FId='5878970') OR (FId='5878969') OR (FId='5880317') ) AND FStatus!=-1
	select * from t_uc_records_101 where ( (FId='5877911') ) AND FStatus!=-1
	select * from t_uc_records_101 where ( (FId='5877911') ) AND FStatus!=-1

 4. 最大的问题是，每次以相同的查询条件加载，耗时好像没有减少。难道Mysql没有查询缓存吗？
# 解决问题 #
合理利用缓存技术，能提高网页访问速度。即便最终没能解决SQL语句的优化，也能在第二次加载时提高速度，带来良好体验。
1. 查询缓存是否开启？
2. 
    mysql> select @@query_cache_type;
	+--------------------+
	| @@query_cache_type |
	+--------------------+
	| ON                   | 
	+--------------------+
	1 row in set (0.01 sec)

 已开启。 
2. 查询缓存是否可用？
    mysql> show variables like 'have_query_cache';
	+------------------+-------+
	| Variable_name    | Value |
	+------------------+-------+
	| have_query_cache | YES   | 
	+------------------+-------+
	1 row in set (0.01 sec)
 可用。 
3. 查询缓存大小？
    mysql> select @@global.query_cache_size;
	+---------------------------+
	| @@global.query_cache_size |
	+---------------------------+
	|                         0 | 
	+---------------------------+
	1 row in set (0.00 sec)
 分配给查询缓存内存为0，就是没分配。  没有任何效果。
4. 设置查询缓存大小。大约10M，不够以后再加。
    mysql> set @@global.query_cache_size=10000000;
Query OK, 0 rows affected, 1 warning (0.00 sec)

 完成这些以后，发现第一次访问速度还是龟速。再刷新，就瞬间加载了。
#     结论#
利用查询缓存来优化查询，能将访问速度减少到200ms以下，能满足当前需求。但是最终的解决之道是对耗时较多、冗余的SQL语句进行优化。
所以，革命尚未成功，同志仍需努力！
