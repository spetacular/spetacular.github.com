---
layout: post
title: Postman 及 Newman 使用开发指南（二）：Postman 环境变量与全局变量
category : nodejs
tagline: "Supporting tagline"
tags : [postman,newman,nodejs]
---

使用API的常见场景是开发人员有自己的本机开发（dev）环境，团队之间共用测试（staging）环境，对外提供生产（production）环境。

本文假设各种环境的链接如下：

```
dev http://localhost
staging http://staging
production https://production
```

开发人员对`/users`接口进行调试时，如果没有环境变量，则可能要不断地输入以下链接进行替换：

```reStructuredText
http://localhost/users
http://staging/users
https://production/users
```

Postman 提供了环境变量（Environment Variable），来解决这个问题。

# 环境变量

## 管理

Postman 环境变量相关按钮放置在Builder页面的右上角。

![postman环境变量相关按钮](/images/2017/postman_env.jpeg)

点击最右的 ⚙️ 图标，可以添加、编辑、查看环境。首先，我们添加一个staging的环境，下属变量url设置为`http://staging` 。

![postman环境变量相关按钮](/images/2017/postman_add_env.jpeg)

再添加dev和production环境。

![postman环境变量相关按钮](/images/2017/postman_env_lists.jpeg)



## 使用示例

我们将环境切换为`dev`，在地址栏输入\{\{url\}\}/users，查看“Code”代码：

```
GET /users HTTP/1.1
Host: localhost
Cache-Control: no-cache
Postman-Token: 9a0ae49e-8983-55f2-7585-a4ea721863da
```

可见，Postman 自动将\{\{url\}}解析为`http://localhost`。



# 全局变量

API测试时，也常有这样的场景：首先要进行登录，登录后获取用户名，而用户名将在后续请求中使用。

Postman 提供了全局变量（Global Variable），来解决类似问题。

# 设置全局变量

至少有三种方式来设置全局变量：

1. Postman 全局变量的按钮放置在Builder页面的右上角，👁️ 图标。

![postman全局变量相关按钮](/images/2017/postman_var.jpeg)

点击“Global”对应的“Edit”即可进入管理页面：

![postman全局变量相关按钮](/images/2017/postman_set_var.jpeg)

2. Pre-request Script
3. Tests

其中方式2和方式3都可以用以下命令设置全局变量：

```javascript
postman.setGlobalVariable("variable_key", "variable_value");
```

# 示例

我们首先通过上节的方式1添加一个全局变量：`leap_year_2016`

![postman全局变量](/images/2017/postman_var_false.jpeg)

我们访问 Postman 官方示例中判断年份是否为闰年的链接：

```
https://postman-echo.com/time/leap?timestamp=2016-10-10
```

然后在Tests里添加如下代码：

```javascript
var jsonData = JSON.parse(responseBody);
postman.setGlobalVariable('leap_year_2016',jsonData.leap) ;
```

![postman全局变量](/images/2017/postman_var_leap.jpeg)

发送请求后，由于2016年是闰年，所以全局变量`leap_year_2016`值应变为true。

查看一下：

![postman全局变量](/images/2017/postman_var_true.jpeg)

`leap_year_2016=true`，符合预期。