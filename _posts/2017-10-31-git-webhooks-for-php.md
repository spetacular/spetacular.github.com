---
layout: post
title: Git webhook 实现自动部署教程
category : git
tagline: "Supporting tagline"
tags : [git,php]
---

Git Hook(钩子) 是 Git 在代码提交、推送、合并等工作流程中引入的事件触发器，其中最常用的场景是代码检查，持续集成，自动部署等。本文主要讲解一下利用 Git webhook 实现自动部署。

# 一、Git hook

Git 的 hook 分为本地仓库 hook 和服务器仓库 hook。

## 本地 hook

本地 hook 通常在代码的 .git/hooks 目录下，如下所示：

```shell
$ hooks git:(master) ls
applypatch-msg.sample     pre-commit.sample         prepare-commit-msg.sample
commit-msg.sample         update.sample
post-update.sample        pre-push.sample
pre-applypatch.sample     pre-rebase.sample
```

默认情况下，这些脚本不会生效。使用时，只需将 `.sample` 后缀去掉，然后赋予脚本执行权限即可。

本地 hook 主要用于代码静态分析、REVIEW、代码规范、命名规范等。

* pre-commit

  提交之前的代码检查，包括是否通过单元测试，静态代码分析 

* prepare-commit-msg

  提交信息之前，可用来生成默认的提交信息

* commit-msg

  提交信息之后，可用来检查提交信息是否符合特定的格式

* post-commit

  提交代码之后，一般用来通知代码已提交。

* post-checkout

  checkout 代码之后，可用来设置工作目录、生成文档、生成静态资源等工作

* post-merge

  合并代码之后，可用来保存 merge 操作中，git 没有保存的信息。

* pre-push

  push 代码之前，可用来检查本次 push 的 commits 是否符合特定的标准。

## 服务器 hook

服务器 hook 指代码传输到服务器时，在服务器端所做的一系列操作。

* pre-receive

  处理 push 操作之前，可以检查本次 push 的 commits 和文件是否符合特定的标准。

* update

  update 与 pre-receive 操作类似，不同的是 pre-receive 只执行一次，而 update 可能执行多次。

* post-receive

  整个提交过程完成之后，可用于更新其它服务或通知用户，比如发邮件告诉开发人员已提交代码，通知持续集成 (Continuous Integration) 服务器部署代码

## webhook

如果 Git 服务部署在自己的服务器上，如用 [GitLab](https://about.gitlab.com/) 搭建一套 Git 服务，则可以使用服务器 hook。如果使用了 [GitHub](https://github.com/)、[Bitbucket](https://bitbucket.org/) 等云端平台，那么只能使用 webhook 来完成脚本。

webhook 本质上属于服务器 hook，因为发送通知的方式是网络请求，因此得名。使用 webhook 的步骤如下：

1. 设置用于接收请求的 URL。
2. 服务器收到 push、pull request、merge、tag 等操作时，会将相应信息发送给步骤 1 里的 URL。
3. URL 对应的程序收到网络请求后，执行自动部署、邮件通知等操作。

# 二、部署 webhook

##环境假设

为叙述方便，我们做如下假设。

| 项目         | 值                                      |
| ---------- | -------------------------------------- |
| 域名         | www.weixinbook.net                     |
| 接收请求的URL   | https://www.weixinbook.net/webhook.php |
| 项目部署目录     | /var/www/weixinbook                    |
| build.sh路径 | /var/www/hooks/build.sh                |
| webhook.php |       /var/www/weixinbook/webhook.php                                 |
| 环境         | nginx php                              |
| 用户组        | www-data                               |


## 部署 SSH 无密码登录

首先需要生存 SSH key。

如果使用 nginx:

```shell
sudo  -u www-data ssh-keygen -t rsa -C "nginx"
```

如果使用 apache:

```shell
sudo -u apache ssh-keygen -t rsa -C "apache"
```

然后复制你的 public key，粘贴到项目的“SSH 公钥”设置里。

```shell
cat ~/.ssh/id_rsa.pub
```

以 GitHub 为例，在 Settings -> Deploy keys，选择 Add deploy key.

![GitHub Deploy Keys](/images/2017/github_deploy_keys.png)

添加完毕后，可以运行 `ssh -T git@github.com` 来验证是否设置成功。首次运行时会看到一条 RSA key 指纹的连接确认信息，输入 `yes` 回车即可。

如果看到下面的信息，就说明设置生效了。

```reStructuredText
Hi username! You've successfully authenticated, but GitHub does not
provide shell access.
```



## webhook.php
webhook.php 用来接收 webhook 请求，一般要做以下操作：

* 监测请求来源合法性。检查方法：密码，AES等。
* 检查是否为特定分支，如 master 分支。
* 启动自动部署脚本，如完成拉取最新代码，重新设置目录权限等。

```php
<?php
$raw_data = file_get_contents("php://input");
$pay_load = json_decode($raw_data,true);
//监测请求来源合法性，
if($pay_load['password'] != 'pass'){
    exit('ok 400');
}
//检查是否为master分支
if($pay_load["ref"] != "refs/heads/master"){
    exit('ok 401');
}

exec('sh /var/www/hooks/build.sh');
```

## build.sh
build.sh 用来执行自动部署的具体操作。一般情况下，不要放在外网可访问的 webroot 目录下。

```shell
#! /bin/bash
SITE_PATH='/var/www/weixinbook'
USER='www-data'
USERGROUP='www-data'
cd $SITE_PATH
git reset --hard origin/master
git clean -f
git pull
git checkout master
chown -R $USER:$USERGROUP $SITE_PATH
```


这样每次在客户端 push 代码，服务器会发请求给 webhook.php，webhook.php 检查通过后，启动 build.sh 进行自动部署。

# 三、结语

自动部署并不难，如果失败，大部分是权限问题。解决的方法很简单：

1. 保证 ssh key 的用户为 www-data
2. 保证 build.sh , webhook.php 以及代码目录的用户为 www-data.

权限问题解决不了，不要草率地全用 root，这样会带来一定的安全风险。