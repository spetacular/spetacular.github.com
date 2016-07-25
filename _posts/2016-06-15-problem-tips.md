---
title: PHP性能监控问题记录之一－安装配置php-apm
layout: post
category : PHP
tagline: "Supporting tagline"
tags : [PHP , 性能]
---
## php-apm是什么
[php-apm](https://github.com/patrickallaert/php-apm)是一个PHP的性能检测工具，它能方便地捕捉到错误信息，并提供错误追踪回溯，获取请求的统计内存、CPU、响应时间的统计信息，并提供[可视化的展示](https://github.com/patrickallaert/php-apm-web)。

## 安装apm
安装apm有两种方式：从PECL安装，或从源码编译。这里采用PECL安装。源码编译见[官方文档](https://github.com/patrickallaert/php-apm#from-source)。

    sudo pecl install apm
	WARNING: channel "pecl.php.net" has updated its protocols, use "pecl channel-update pecl.php.net" to update
	downloading APM-2.0.5.tgz ...
	Starting to download APM-2.0.5.tgz (31,484 bytes)
	.........done: 31,484 bytes
	14 source files, building
	running: phpize
	Configuring for:
	PHP Api Version:         20131106
	Zend Module Api No:      20131226
	Zend Extension Api No:   220131226
	Enable Sqlite3 support [yes] : no
	Enable MariaDB/MySQL support [yes] : yes
	Enable Socket support [yes] : yes
	Enable Statsd support [yes] : yes

出现了如下错误：

	configure: error: Cannot find MySQL header files
	ERROR: `/tmp/pear/temp/APM/configure --with-php-config=/usr/bin/php-config --with-sqlite3=no --with-mysql --enable-socket=yes --enable-statsd=yes' failed

需要安装下mysql dev

    sudo apt-get install mysql-client libmysqlclient-dev

## 配置apm
在php.ini上配置apm。apm.so依赖于json.so，如果之前没有加载，则应先加载。

    extension=json.so
	extension=apm.so
	;;;;;;;;;;;;;;;;;;;
	; Module Settings ;
	;;;;;;;;;;;;;;;;;;;

	;apm
	apm.mysql_enabled=1
	; Error reporting level specific to the MariaDB/MySQL driver, same level as for PHP's *error_reporting*
	apm.mysql_error_reporting=E_ALL|E_STRICT
	apm.mysql_stats_enabled=1
	apm.mysql_host=localhost
	apm.mysql_port=3306
	apm.mysql_user=apm
	apm.mysql_pass=apm
	apm.mysql_db=apm
	apm.socket_enabled=0
	apm.socket_stats_enabled=0

然后重启php，例如：
 
    service php5-fpm restart

## 部署apm-web
apm提供了一个web端查看的项目，地址为：[https://github.com/patrickallaert/php-apm-web](https://github.com/patrickallaert/php-apm-web)。

将[https://github.com/patrickallaert/php-apm-web/archive/master.zip](https://github.com/patrickallaert/php-apm-web/archive/master.zip)解压后上传到网站根目录，或者如果使用git，可以clone到网站根目录下：

    git clone https://github.com/patrickallaert/php-apm-web.git apm-web

然后编辑config/db.php文件。如果选用mysql，可以如下配置：

    return new PDO("mysql:host=localhost;dbname=apm", "apm", "apm");

最后的效果如下：

返回Error的请求列表
![返回Error的请求列表](http://spetacular.github.io/images/2016/web_apm_faulty.png)

点击每一列，都会出现请求的URL、时间、内存、CPU使用情况，以及错误信息。
![错误信息](http://spetacular.github.io/images/2016/web_apm_e_error.png)

点击错误信息，会出现该错误的Stacktrace。
![错误追踪回溯](http://spetacular.github.io/images/2016/web_apm_stacktrace.png)

还有详细的访问统计日志。
![访问日志](http://spetacular.github.io/images/2016/web_apm_stats.png)

## 常见问题

### 问题1 部署后无数据，且扩展未加载。

检测php.ini文件，看是否有apm扩展。如果出现类似如下的信息，则说明扩展已加载。
![php.ini文件](http://spetacular.github.io/images/2016/php_ini_apm.png)

### 问题2 部署后无数据，扩展已加载。

mysql_stats_enabled默认值为0，这时是不收集的。可以将设置apm.mysql_stats_enabled=1。

