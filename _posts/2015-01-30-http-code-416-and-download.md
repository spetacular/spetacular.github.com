---
layout: post
title: HTTP 416错误与断点续传
category : php
tagline: "Supporting tagline"
tags : [http, 断点续传]
---
看房团 App 1.8.2更新版本时，偶然发现无法从1.8.1升级到1.8.2，于是探究一番，发现大有深意。

##问题
升级，无非是下载新版软件包，再进行安装。无法更新，最直接的反应是下载链接是不是404了？

那抓下包吧。

![fiddler抓包记录](http://spetacular.github.io/images/2015-01-30/fiddler-session.jpg)

出人意料的416。

把地址放到浏览器中，能成功下载；传到手机上，也能安装。说明不是下载链接的问题。 

先看下HTTP 416错误代表什么吧？

`所请求的范围无法满足 (Requested Range not satisfiable)`

 看了不明觉厉，因为从没遇见过。

 

##探索
问了下客户端的同学，发现下载使用的是HttpURLConnection，于是Google一下，得到一些关键信息： 

    HTTP response code: 416是由于读取文件时设置的Range有误造成的，具体的说就是下面这行代码有误：
    httpConnection.setRequestProperty("RANGE", "bytes=1024-");
    这个RANGE显然不能超出文件的size

 而客户端设置的RANGE为文件大小。

 

试想，文件存在远程服务器上，如何知道文件大小？

至少要发起两次请求。第一次请求，不需要下载整个文件，只需要获得Response的Content-Length大小；第二次请求，将Content-Length值写进RANGE，实现下载。 

看第一次请求时，发现如下：

![range bytes=0](http://spetacular.github.io/images/2015-01-30/range-bytes-zero.png)

这是罪魁祸首！ 

为了验证，将Range去掉，再构造请求，一切OK。

![去掉Range](http://spetacular.github.io/images/2015-01-30/remove-range.jpg)

 

##结论
造成返回码416的原因，是设置的Range有误。解决办法也很简单，将第一次请求时的Range去掉。

       //删掉之后，整个世界都清净了！
       conn.setRequestProperty("Range", "bytes=" + startPosition);// startPosition=0
  

##启示
解决bug并不难，难的是版本已发，鞭长莫及。这时我想到的解决办法是：用其它链接替换，或服务器做一个中转。但前者需要更换CDN提供商，后者需要接入层服务器的支持，都没有着眼于问题的本质。

问题的本质是，无论CDN服务器支持与否，客户端请求的方式都存在不足之处，在下一个版本中修正即可。而且从报表上看，用户自发地通过其它途径升级到了最新版本！

程序员感叹：不要试图用代码解决所有问题。

讨论
下载地址是CDN地址，莫非CDN不支持断点续传？

恰好相反，416正是支持断点续传的标志。服务器得到一个Range之后，需要对它的取值进行检验，包括： 

开始位置非负

结束位置需要大于开始位置

开始位置需要小于文件长度减一 (因为这里的位置索引是从0开始的)

若结束位置大于文件长度减一，则需要把它的值设置为文件长度减一

如果Range的取值不合法，则需要终止程序并告知浏览器：

        header('HTTP/1.1 416 Requested Range Not Satisfiable');

在本文的例子中，CDN服务器尽职尽责地告诉客户端，你的请求错了！
 

CDN服务器没有错，但可以采用稍微柔性的处理方法。Range为0，可以啊，就从头开始下载吧。

七牛云存储就是这么干的。
 

PS：别问我最后一句是怎么来的，七牛给过我一顶太阳帽^_^。
 

参考网址：

[http://www.checkupdown.com/status/E416_cn.html](http://www.checkupdown.com/status/E416_cn.html)

[http://blog.csdn.net/zollty/article/details/9176829](http://blog.csdn.net/zollty/article/details/9176829)

[http://www.pureweber.com/article/resumable-download/](http://www.pureweber.com/article/resumable-download/)