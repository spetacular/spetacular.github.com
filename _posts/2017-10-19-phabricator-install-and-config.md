---
layout: post
title: Phabricator 入门教程
category : nodejs
tagline: "Supporting tagline"
tags : [Phabricator,linux]
---

[Phabricator](https://phacility.com/phabricator/) 是一款用于敏捷开发的项目管理软件，它集成了众多实用功能，包括：

* 代码管理：添加 Git, Mercurial 和 SVN 仓库，查看源码，review 代码
* bug 追踪：测试人员、开发人员协同工作
* 项目管理：项目的启动、进展、完成
* 工作板：所有任务一目了然
* wiki：构建文档
* 任务系统：创建任务、指派任务、完成任务、增加或降低优先级
* 博客系统：甚至可以写博客

![phabricator介绍](/images/2017/phabricator_intro.png)



这里介绍如何从零开始，完成 Phabricator 的安装配置，设置数据库和发送邮件配置，仅供参考。

# 前提条件

Phabricator 的运行环境是 php，数据库采用 mysql，web服务器可选 nginx、apache、lighttpd等。

如果操作系统为 ubuntu ，希望快速安装，可以拷贝 [install_ubuntu.sh](https://secure.phabricator.com/source/phabricator/browse/master/scripts/install/install_ubuntu.sh) 脚本，一键安装 php5、apache、mysql-server。

这里安装 php5 (包括所需扩展)、nginx、mysql-server。

```shell
sudo apt-get update
sudo apt-get install php5 php5-mysql php5-gd php5-dev php5-curl php-apc php5-cli php5-json nginx mysql-server
```

## Tips：PHP 7 的兼容性

如果你的环境是PHP 7.0，那么恭喜你，Phabricator 无法运行在 PHP 7.0 下。

Phabricator 支持 5.x 版本以及 7.1 以上版本，唯独不支持 7.0。

据[官方声明](https://secure.phabricator.com/T12101)，Phabricator 依赖的异步信号处理特性，在 7.0 版本被移除了，但是在7.1 版本又加上了。

使用 PHP 7.0 的用户，可以降级到 PHP 5 或升级到 PHP 7.1，或者用 Docker。

# 安装

Phabricator 需要 3 个仓库的代码：arcanist、libphutil、phabricator。其中phabricator是项目的主体，arcanist 是 Phabricator 的命令行工具，libphutil 是Phabricator 的实用工具集，包括文件系统、markdown解析、守护进程等。

假设 `webroot` 目录为 `/var/www`，则执行如下命令：

```shell
cd /var/www
mkdir phabricator && cd $_
git clone https://github.com/phacility/libphutil.git
git clone https://github.com/phacility/arcanist.git
git clone https://github.com/phacility/phabricator.git
```

保证 arcanist、libphutil、phabricator 目录为并列关系。

```shell
root@localhost:/var/www/phabricator# ls
arcanist  libphutil  phabricator
```

配置 nginx：

其中 root 指向 phabricator 目录的 webroot。

```nginx
server {
  server_name phabricator.example.com;
  root        /var/www/phabricator/phabricator/webroot;

  location / {
    index index.php;
    rewrite ^/(.*)$ /index.php?__path__=/$1 last;
  }

  location /index.php {
    fastcgi_pass   localhost:9000;
    fastcgi_index   index.php;

    #required if PHP was built with --enable-force-cgi-redirect
    fastcgi_param  REDIRECT_STATUS    200;

    #variables to make the $_SERVER populate in PHP
    fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
    fastcgi_param  QUERY_STRING       $query_string;
    fastcgi_param  REQUEST_METHOD     $request_method;
    fastcgi_param  CONTENT_TYPE       $content_type;
    fastcgi_param  CONTENT_LENGTH     $content_length;

    fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;

    fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
    fastcgi_param  SERVER_SOFTWARE    nginx/$nginx_version;

    fastcgi_param  REMOTE_ADDR        $remote_addr;
  }
}
```

# 配置

## 数据库

首先要设置用于 Phabricator 连接的数据库配置。

```shell
cd /var/www/phabricator/phabricator
phabricator/ $ ./bin/config set mysql.host localhost
phabricator/ $ ./bin/config set mysql.user root
phabricator/ $ ./bin/config set mysql.pass 123456
```

然后执行：

```shell
phabricator/ $ ./bin/storage upgrade
```

## 邮件

Phabricator 支持的[邮件系统](https://secure.phabricator.com/book/phabricator/article/configuring_outbound_email/)很多，如Mailgun、Amazon SES、SendGrid、SMTP。这里以 SMTP 为例。

有两种方式配置邮件发送。

### 1. 配置文件

修改 conf/local/local.json ，加入如下配置：

```json
{
  "phpmailer.smtp-protocol": "SSL",
  "phpmailer.smtp-password": "密码",
  "phpmailer.smtp-user": "邮箱地址",
  "phpmailer.smtp-port": 端口号,
  "phpmailer.smtp-host": "邮件服务器"
}
```

### 2. 命令行

用 bin/config 命令进行设置，如：

```shell
phabricator/ $ ./bin/config set phpmailer.smtp-protocol ssl
phabricator/ $ phabricator/ $ ./bin/config set phpmailer.smtp-password 密码
phabricator/ $ ./bin/config set phpmailer.smtp-user 邮箱地址
phabricator/ $ ./bin/config set phpmailer.smtp-port 端口号
phabricator/ $ ./bin/config set phpmailer.smtp-host 邮件服务器
```

### 验证

配置完成后，可以用命令行测试邮件发送配置是否正确：

```shell
phabricator/ $ ./bin/mail list-outbound   # 列出所有邮件
phabricator/ $ ./bin/mail show-outbound   # 显示某条邮件
phabricator/ $ ./bin/mail send-test       # 发送测试邮件
```

例如给 david 发送 主题为 hello 的邮件，正文在 body.txt 里：

```shell
phabricator/ $ ./bin/mail send-test --to david --subject 'hello' < ./body.txt
Reading message body from stdin...
Mail sent! You can view details by running this command:

    phabricator/ $ ./bin/mail show-outbound --id 37773
```

执行 `./bin/mail show-outbound --id 37773` 可以看到此处发送的调试信息。

```
PROPERTIES
ID: 37773
Status: sent
Related PHID:
Message:

PARAMETERS
sensitive: 1
cc: []
subject: hello
is-bulk:
mailtags: []
...............
```

这些命令对调试邮件配置和开发新的邮件适配器 (Adapter) 很有用处。



#结语

至此，Phabricator 可以使用了。更详细的功能和配置请参考  [Phabricator User Documentation](https://secure.phabricator.com/book/phabricator/)