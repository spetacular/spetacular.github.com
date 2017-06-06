---
layout: post
title: Postman 及 Newman 使用开发指南（六）：利用 Newman 进行冒烟测试
category : nodejs
tagline: "Supporting tagline"
tags : [postman,newman,nodejs]
---

大家提到“冒烟测试”，大部分人会援引[微软的定义](https://msdn.microsoft.com/zh-cn/library/ms182613(v=vs.90).aspx)： 

> 在软件中，“冒烟测试”这一术语描述的是在将代码更改签入到产品的源树中之前对这些更改进行验证的过程。在检查了代码后，冒烟测试是确定和修复软件缺陷的最经济有效的方法。冒烟测试设计用于确认代码中的更改会按预期运行，且不会破坏整个版本的稳定性。
>
> “冒烟测试”这一术语源自硬件行业。该术语源于此做法：对一个硬件或硬件组件进行更改或修复后，直接给设备加电。如果没有冒烟，则该组件就通过了测试。

对于 API 接口来说，冒烟测试就是跑一遍测试，看看所有的返回结果是否符合预期。判断一次冒烟测试是否通过的标准，可以简化一下两条：

* 网络请求正常
* 返回内容符合预期

本节我们用 Newman 编写冒烟测试案例，并生成自定义报表，附带邮件发送功能。本节的例子完整代码参考 [https://github.com/spetacular/newman-smoking-test](https://github.com/spetacular/newman-smoking-test)

# nodejs 脚本

脚本用到了两个插件：[Newman](https://github.com/postmanlabs/newman) 和 [Nodemailer](https://nodemailer.com/) 。

```
npm install newman --save
npm install nodemailer --save
```

脚本 smoke.js:

将 `---` 包含的部分换成正确的配置即可。
<script src="https://gist.github.com/spetacular/4e60770ebecda84064aacb123531b670.js"></script>

# 自定义 HTML 模板

由于大部分邮件客户端只支持 inline 的 CSS 样式，所以我们将样式写到元素里。

template.hbs 文件：

<script src="https://gist.github.com/spetacular/8b9375c373b8339cb2904cbcd56cffaa.js"></script>

最后发送邮件的效果如下：

![newman冒烟测试效果](/images/2017/postman_newman_mail.jpeg)



完整代码参考 [https://github.com/spetacular/newman-smoking-test](https://github.com/spetacular/newman-smoking-test)