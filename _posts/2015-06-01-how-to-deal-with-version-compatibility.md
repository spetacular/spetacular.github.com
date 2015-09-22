---
layout: post
title: Restful接口设计：如何做版本兼容
category : 接口
tagline: "Supporting tagline"
tags : [接口, api, 版本控制]
---

现在App大多采用Restful接口，与服务器完成资源交换。这种实现方式隐藏了服务器端的具体实现，做到了对App端的透明服务。服务器端如何变更，甚至换一种语言实现，只要对外接口不变，App依然不受影响。   

App的升级过程中，版本兼容是一个值得关注的问题，因为App要升级，服务器端的Restful接口也要进化，于是版本兼容要同时兼顾App和服务器端。   

#一个例子#
版本兼容是向下兼容。接口升级后要保证低版本App能正常工作。   

例如“App 1.0”使用了下面接口完成修改用户信息功能。   

	GET api.demo.com/modify_userinfo

采用GET方式提交用户输入。后来发现该接口可被CSRF攻击。

这时要做两件事：

第一，服务器接口要为新的“App 1.1”提供升级，需要将GET方式改为POST，并且增加Token验证。这是CSRF的一般防范方法，具体请参看 [《CSRF简单介绍及利用方法》](http://drops.wooyun.org/papers/155 "CSRF简单介绍及利用方法")

第二，服务器接口要为旧的“App 1.0”版本修复漏洞。但这时App已发布，不可能修改了。这时只能服务器端来改。例如限制user-agent为"App 1.0"的请求才有效，用户通过浏览器提交的请求无效。这样在一定程度上减少了CSRF的危害。

#版本兼容的两种情况#
从上面的例子来看，版本兼容可分为两种：

大版本，指版本升级，接口的输入输出可能发生变化。

小版本，指版本修复，只针对某个版本修改接口，其它接口保持不变。

这两种情况都很常见。App未发布时，服务器接口从1.0升级到2.0，这时App开发者可以配合一下，使用2.0的接口；但对于已发布的App版本，只能由服务器端针对特定的App版本来修复漏洞。

#如何做#
App开发者需要配合，在请求接口时传入以下两项信息：

1. App版本标识，一般包括App名称、版本、平台（Android、IOS等）、SDK版本等。一般放在user-agent里，例如微信的user-agent如下：

	Mozilla/5.0 (iPhone; CPU iPhone OS 5_1 like Mac OS X) AppleWebKit/534.46 (KHTML, like Gecko) Mobile/9B176 MicroMessenger/6.2.2

2. 接口版本标识，指采用接口的版本，例如可以在Request Header里加入majarversion=2.0，表明使用的是2.0的接口。

服务器端开发者需要根据App版本和接口版本标识，来提供向下兼容



