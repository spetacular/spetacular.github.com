---
layout: post
title: 开发知识点记录
category : linux
tagline: "Supporting tagline"
tags : [linux]
---

# cron 任务没执行的原因

查看 cron 日志，日志路径一般在：

```
/var/log/cron.log
```

或者

```
/var/log/syslog
```

这样就能看到类似这样的错误：

> Error: bad username; while reading /etc/crontab
> (*system*) ERROR (Syntax error, this crontab file will be ignored)

然后按图索骥，查找失败原因。

# cron 启动、关闭、重启、重载、查看服务状态
```
	service cron start    //启动服务
　　	service cron stop     //关闭服务
　　	service cron restart  //重启服务
　　	service cron reload   //重新载入配置
　　	service cron status   //查看服务状态 
```

# 命令行输错命令如何快速撤销？
最笨的方法可以按删除键，一个一个删除；
![撤销命令](/images/2016/withdraw-input-1.gif)
最快的办法是Ctrl + U，一键清理。
![撤销命令](/images/2016/withdraw-input-2.gif)
