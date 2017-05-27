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

# docker显示容器命令(container CMD)详情

<script src="https://gist.github.com/spetacular/1df9fba67d5854f4ad203b19c9cc919c.js"></script>

# php生成形容'HH:MM:SS'格式的时间

```
gmstrftime('%H:%M:%S',$time)
```

例如

```
echo gmstrftime('%H:%M:%S',3786);
//Output: 01:03:06
```

# chrome 下上传图片/文件慢的解决办法

最近用flow.js做的上传图片功能，突然变得很慢，要10秒左右才能打开上传对话框。查了一下，是mac chrome的一个bug。设置图片格式时，如果采用如下写法：

```
<input type="file" accept="image/*">
```

则会重现。有网友反映，windows 10, chrome 53下也会有此问题。解决办法是，将image/*换为具体的格式：

```
<input type="file" accept="image/png, image/jpeg, image/gif">
```

参考：[open dialog so slow with Chrome](http://stackoverflow.com/questions/39187857/inputfile-accept-image-open-dialog-so-slow-with-chrome)

# redis 按前缀/模式批量删除keys
有两种情况：
1.在redis外部，可以用 xargs；
2.在redis内部，可以用 eval 执行脚本
示例：
先创建一些key。
```
127.0.0.1:6379> set david1 1
OK
127.0.0.1:6379> set david2 2
OK
127.0.0.1:6379> keys david*
1) "david2"
2) "david1"
```
方法1:
```
redis-cli keys david* | xargs redis-cli del
```
方法2:
进入 redis 命令行。
```
127.0.0.1:6379> eval "local keys = redis.call('keys',KEYS[1]) for i,v in ipairs(keys) do redis.call('del',v) end" 1 david*
(nil)
```
两种方法的结果如下：
```
127.0.0.1:6379> keys david*
(empty list or set)
```

