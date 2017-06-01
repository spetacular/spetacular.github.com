---
layout: post
title: MySQL 1045 Access denied 和 1449 The user specified as a definer does not exist 错误处理
category : mysql
tagline: "Supporting tagline"
tags : [mysql]
---
MySQL 使用新建用户查询时，如果数据库中有 view，可能会出现这样的错误：

```mysql
SQLSTATE[28000]: Invalid authorization specification: 1045 Access denied for user 'user'@'10.174.68.21' (using password: YES)
```

或者

```mysql
SQLSTATE[HY000]: General error: 1449 The user specified as a definer ('db_prod'@'%') does not exist
```

其表现是：

* 涉及到 table 的查询都正常；
* 涉及到 view 的查询都报错。

大多数情况下，出现此问题的根源是view definer设置不当。

# 查看所有的 definer

可以先检查下所有 view 的 definer。

```mysql
select DEFINER from information_schema.VIEWS;                                          +---------------------------+
| DEFINER                   |
+---------------------------+
| db_prod@%                 |
| db_prod@%                 |
| db_prod@10.174.68.21      |
| root@127.0.0.1            |
+---------------------------+
4 rows in set (0.00 sec)
```

可以发现，某些 view 的 definer 为db_prod@% 。

# 查看view的definer

进而可以查看单个 view 的 definer。

```mysql
SHOW CREATE VIEW viewname
CREATE ALGORITHM=UNDEFINED DEFINER=`db_prod`@`%` SQL SECURITY DEFINER VIEW `viewname` AS select * from tablename;
```

# 创建用户时需要注意的事项

如果 view 的 definer 与当前的用户不一致，可以修改或删除用户。

```mysql
删除用户
DROP USER 'db_prod'@'10.174.68.21';
修改 definer
UPDATE `mysql`.`proc` p SET definer = 'root@localhost' WHERE definer='root@foobar' AND db='dbname';
```

在创建用户时，可以用如下 SQL.

```mysql
创建用户
CREATE USER 'db_prod'@'%' IDENTIFIED BY '123456';
授权
GRANT ALL ON dbname.* TO 'db_prod'@'%';
```

关键是保持 DEFINER 和创建用户时的用户名完全一致。

# 参考资料

[MySql: Some queries produce: SQLSTATE28000: Invalid authorization specification: 1045 Access denied for user](https://stackoverflow.com/questions/35662051/mysql-some-queries-produce-sqlstate28000-invalid-authorization-specificatio)

[mysql如何修改所有的definer](http://www.cnblogs.com/zejin2008/p/4767531.html)

