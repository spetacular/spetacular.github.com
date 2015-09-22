---
layout: post
title: PHP扩展开发Demo
category : php
tagline: "Supporting tagline"
tags : [php扩展]
---
为了深入学习PHP，就从一个简单的PHP扩展开始，了解一下制作过程。

# 准备工作 #
在制作PHP扩展之前，需要两个条件：

1.正常安装的PHP环境

2.PHP源码

这里我的PHP安装在/usr/local/php5.3.10目录，源码在/data/webdev/php-5.3.10

# 生成扩展基本框架 #
运行以下命令：

	cd /data/webdev/php-5.3.10/ext
	./ext_skel helloyo
	vi helloyo/config.m4

编辑文件，

	dnl PHP_ARG_ENABLE(helloyo, whether to enable helloyo support,
	dnl Make sure that the comment is aligned:
	dnl [  --enable-helloyo           Enable helloyo support])

其中dnl为注释，删掉dnl及第二行，最后的效果如下：

	PHP_ARG_ENABLE(helloyo, whether to enable helloyo support,
	[  --enable-helloyo           Enable helloyo support])

保存退出。
# 编写C代码 #
进入helloyo目录，用ls命令看下文件列表：

	/data/webdev/php-5.3.10/ext/helloyo # ls
	config.m4  config.w32  CREDITS  EXPERIMENTAL  helloyo.c  helloyo.php  php_helloyo.h  .svnignore  tests

编辑php\_helloyo.h，加入say\_hello函数入口

	vi php_helloyo.h
 
![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/vi_php_helloyo.png)

编辑helloyo.c，需要修改两处内容

	vi helloyo.c

1.为保证say\_hello外部可以使用，需要加入在zend\_function\_entry helloyo\_functions数组里加入一行：

	PHP_FE(say_hello,        NULL)           /* say hello to someone. */

![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/helloyo_function.png)

 
2.需要实现say_hello函数。在文件末尾加入以下代码：

	PHP_FUNCTION(say_hello)
	{
	
	        char *arg = NULL;
	        int arg_len, len;
	        char *strg;
	
	        if (zend_parse_parameters(ZEND_NUM_ARGS() TSRMLS_CC, "s", &arg, &arg_len) == FAILURE) {
	                return;
	        }
	
	        len = spprintf(&strg, 0, "Hello,%s!\n", arg);
	        RETURN_STRINGL(strg, len, 0);
	}

保存退出。

![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/say_hello_function.png)

 
# 编译安装 #
1.在helloyo目录下运行phpize命令。phpize命令用来为php扩展的构建环境作准备工作。php官方文档中的说明如下：
	
> The phpize command is used to prepare the build environment for a PHP extension. 
   
	/data/webdev/php-5.3.10/ext/helloyo # /usr/local/php5.3.10/bin/phpize   
	Configuring for:   
	PHP Api Version:         20090626  
	Zend Module Api No:      20090626  
	Zend Extension Api No:   220090626  

完成后，目录下会生成configure文件：

![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/helloyo_ls.png)

2.然后运行如下命令

	./configure --with-php-config=/usr/local/php5.3.10/bin/php-config
	make
	make test
	make install

这样就会生成扩展helloyo.so了。其中helloyo.so的生成目录如下：

	/data/webdev/php-5.3.10/ext/helloyo # make install
	Installing shared extensions:     /usr/local/php5.3.10/lib/php/extensions/no-debug-non-zts-20090626/

如果不知道扩展目录，可以用phpinfo命令查看extension_dir路径，如下图所示：
 
![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/extension_dir.png)

3.编辑php.ini，将扩展加入进去

只要找到php.ini，在文件中加入一行配置即可：

	extension=helloyo.so

问题时，在某种情况下，目录中有多个php.ini存在，如下

![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/find_php_ini.png)
 
这三个php.ini哪个才是真正的配置文件呢？  
挨个试~~~~ Good Idea！  
但是，可以用phpinfo来看，一目了然。  

![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/php_info_path.png)
 
运行以下命令，编辑php.ini：

	/usr/local/php5.3.10 # vi ./etc/php.ini

最终效果如下：

![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/add_php_extension.png)

# 验证与运行 #
1.模块是否加载

	/usr/local/php5.3.10 # ./bin/php –m

结果如下：

![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/php_show_modules.png) 

可以看到helloyo已经被加载了。

2.cli命令行
命令如下：

	/usr/local/php5.3.10 # ./bin/php -r "echo say_hello('davidyan');"
	Hello,davidyan!

效果如下：
 
![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/php_cli.png) 

3.cgi网页
在网页内查看，需要重启apache。在phpinfo里可以看到模块已加载了。

![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/cgi_phpinfo.png) 

再写一小段程序：

	public function ActionSayHello(){
	$name = $_GET['name'];
	echo say_hello($name);
	}

访问sayhello?name=davidyan，结果如下：
 
![PHP扩展开发](http://spetacular.github.io/images/2014-10-10/demo.png) 

# 小结 #
通过step by step的操作，了解了php扩展的开发方法。由易而难，以后遇到扩展需要开发时，就知道从何下手了。
