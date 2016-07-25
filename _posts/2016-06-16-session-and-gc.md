---
title: PHP性能监控问题记录之二－session与gc过程
layout: post
category : PHP
tagline: "Supporting tagline"
tags : [PHP , 性能]
---

## session_start与gc垃圾回收过程
在调性能时，偶然发现有个session函数（ThinkPHP/Common/functions.php）耗时很大。查了一下，ThinkPHP框架默认开启了session，就是每次请求都会调用session_start。 这里面存在问题。  
因为在PHP中, 如果使用file_handler作为Session的save handler, 那么就有概率在每次session_start的时候运行Session的Gc过程。详见[鸟哥的分析](http://www.laruence.com/2011/03/29/1949.html)。  
这样就造成每隔一段时间，session函数这里触发了Gc过程，就变慢了。  

解决方法是将session_start默认关闭，需要时再打开。

    'SESSION_AUTO_START'=>false