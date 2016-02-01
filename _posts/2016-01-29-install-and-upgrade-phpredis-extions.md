---
title: Redis技巧:phpredis扩展安装与升级
layout: post
category : redis
tagline: "Supporting tagline"
tags : [redis]
---

为了使用zscan来处理有序集合（Sorted Set）按模式获取数据，需要将phpredis扩展从2.2.4升级到2.2.5以上（最新版本为2.2.7）。

安装和升级方法：

1.下载安装扩展

网址：https://pecl.php.net/package/redis


      wget https://pecl.php.net/get/redis-2.2.7.tgz
      tar zxvf redis-2.2.7.tgz
      cd redis-2.2.7
      phpize
      ./configure
      make && make install

2.检查：

      php -i | grep Redis
      Redis Support => enabled
      Redis Version => 2.2.7

安装或升级成功。

3.重启php5-fpm

      service php5-fpm restart

