---
layout: post
title: 腾讯云操作系统镜像使用方法
---

最近一个项目部署在腾讯云服务器(http://www.qcloud.com/)上，在服务器配置时使用了操作系统镜像功能，感觉很赞。因此把使用方法记录下来，供需要的同事参考。
为什么需要镜像功能
写文档时，Ctrl C 加 Ctrl V，多方便啊。
有需要多台服务器时，只要配置一台的各种软件及服务，其它的机器都复制过去，是不是也很省事啊？
这就是镜像功能存在的意义。

使用流程
先解释两个名词：
1.	参考服务器。指用于制作镜像的机器，其配置将被复制到其它机器上。
2.	目标服务器。指需要镜像的机器，将复杂参考服务器上的配置。

流程如下：

![创建腾讯云操作系统镜像](http://spetacular.github.io/images/2014-10-21/work_flow.png)
<center>图1</center>

1.	配置参考服务器
安装需要的软件，完成配置。
2.	制作参考服务器镜像
这里需要注意两点：
	只对系统盘做镜像，数据盘需要另外处理。
	制作过程中需要关机，整个过程持续十几分钟。

具体过程如下图所示。
 
![第一步：服务器管理，制作镜像](http://spetacular.github.io/images/2014-10-21/step_1_server_management.jpg)
<center>图2</center>

![第二步：填写参数](http://spetacular.github.io/images/2014-10-21/step_2_make_image.png)
<center>图3</center>

制作镜像时，需要知道那个是系统盘：

	# df -lh
	Filesystem            Size  Used Avail Use% Mounted on
	/dev/vda1             7.9G  2.9G  4.6G  39% /
	udev                  7.9G  148K  7.9G   1% /dev
	/dev/vdb1             493G  245M  467G   1% /data

但没有图3中/dev/xvda。咨询后，得到如下信息：
/dev/xvda是xen虚拟化的系统盘
/dev/vda是kvm虚拟化的系统盘
这样系统盘就是/dev/vda1，而数据盘是/dev/vdb1

经过十几分钟，在镜像管理页面可以看到已制作的镜像。

![镜像列表](http://spetacular.github.io/images/2014-10-21/done.jpg)
<center>图4</center>

3.	目标服务器重装操作系统
这里腾讯云有完整的教程，不再赘述。传送门
（http://wiki.qcloud.com/wiki/%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E7%AE%A1%E7%90%86#6._.E9.87.8D.E8.A3.85.E6.93.8D.E4.BD.9C.E7.B3.BB.E7.BB.9F）

收尾工作
在目标服务器重装操作系统后，工作基本结束。不过如果服务器挂载了数据盘，还要做下最后的收尾工作。
使用命令“mkdir /mydata”创建mydata目录，再通过“mount /dev/xvdb1 /mydata”命令手动挂载新分区后，用“df -h”命令查看，出现以下信息说明挂载成功，即可以查看到数据盘了。
 
![挂载数据盘](http://spetacular.github.io/images/2014-10-21/show_df.png)
<center>图5</center>

至此，已成功将参考服务器的镜像复制到目标服务器，Enjoy。
