---
layout: post
title: Postman 及 Newman 使用开发指南（一）：Postman 简介
category : nodejs
tagline: "Supporting tagline"
tags : [postman,newman,nodejs]
---

[Postman](https://www.getpostman.com/) 是一款帮助开发者分享、测试、文档化、监控API接口的工具。它最开始是一个 [Chrome插件](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) ，但现在提供了 Mac、Windwos、Linux、Chrome 全平台的支持。

# 简介

## 下载安装

Postman 目前提供了全平台的版本，读者可以到以下地址进行下载安装。

[https://www.getpostman.com/](https://www.getpostman.com/)

安装启动后，界面如下：

![postman界面](/images/2017/postman_ui.jpeg)



## 界面介绍

### 顶部

Show/Hide Sidebar: 显示或隐藏边栏

Runner: 打开接口测试工具Runner；

Import: 导入postman数据，包括postman集合、环境变量、curl命令等文件；

Open: 打开新窗口

Builder: 请求构造器，为主工作区域

Team Library：团队工作区域

SYNC：同步功能，登录后可将本地数据集上传到云端，以便备份、分享或异处使用。

Sign In：登录功能。

设置：设置，文档，官方支持中心

### 边栏

Filter：搜索框，提供关键字检索

History：请求历史，按时间倒序

Collections：postman集合，包含多个API。这是一个很重要的概念，后续的Runner就是基于集合进行的。

### Builder

请求构造器，使用方式类似于浏览器。最简单的使用方式就是在地址栏输入地址，点击“Send”按钮，稍后响应窗口会显示响应结果。

postman可指定如下请求构造部分：

1. 请求方式：GET、POST、PUT、PATCH、DELETE、COPY、HEAD、OPTIONS、LINK、UNLINK、PURGE、LOCK、UNLOCK、PROPFIND、VIEW
2. 参数：支持可视化编辑、批量编辑
3. 认证方式：Authorization方式，支持No Auth、Basic Auth、Digest Auth、OAuth 1.0、OAuth 2.0、Hawk Authentication、AWS Signature。其中No Auth是利用Cookie认证。
4. headers：设置请求头
5. Body：当请求为POST方式时，可以设置Body。共有四种情况：
   * form-data：模拟表单提交
   * x-www-form-urlencoded：模拟ajax请求
   * raw：原始格式，支持多种格式，包括Text、JSON、Javascript、XML、HTML等
   * binary：上传文件
6. Pre-request Script: 提交请求之前执行的脚本。
7. Tests：对响应结果做测试。

postman可以查看响应结果：

1. Body： 响应内容，支持JSON、XML美化高亮方式。
2. Cookies：返回Cookies
3. Headers：响应头信息。
4. Tests：对响应结果的单元测试。



# 功能

## 生成Code

Builder页面的右上角，有“Cookie”、”Code“字样，点击”Code“，可以查看各种语言生成的请求代码。

![生成code](/images/2017/postman_code.jpeg)



目前支持大部分常用语言，包括HTTP、C、cURL、C#、Go、Java、Javascript、NodeJS、Object-C、OCaml、PHP、Python、Ruby、Shell、Swift等。



## 管理Cookie

Builder页面的右上角，有“Cookie”、”Code“字样，点击”Cookie“，可以进入管理Cookie页面。「注意：某些版本的postman还未集成此功能，如Chrome插件」

![管理Cookie](/images/2017/postman_cookie.jpeg)



## 单元测试

请求一个接口后，我们可能希望这个接口能满足一定的条件，如：

* HTTP返回码为200
* 符合一定的JSON格式
* 响应时间少于200ms

postman的tests功能可以满足此需求。

![单元测试](/images/2017/postman_tests.jpeg)



代码：

```javascript
tests["Status code is 200"] = responseCode.code === 200;
var jsonData = JSON.parse(responseBody);
tests["2016 is a leap year"] = jsonData.leap === true;
```

我们希望HTTP返回码为200。2016年是闰年，所以我们希望返回leap=true。

而响应窗口的Tests选项卡，显示2条测试都通过，说明接口一切正常。