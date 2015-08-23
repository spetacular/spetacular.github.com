---
layout: post
title: Nginx Sticky的使用及踩过的坑
---
#什么是Sticky？#
为了理解Sticky的工作原理，我们可以先考虑一个问题：负载均衡怎么做？

DNS解析，在域名解析时分配给不同的服务器IP；

IP Hash，根据客户端的IP，将请求分配到不同的服务器上；

cookie，服务器给客户端下发一个cookie，具有特定cookie的请求会分配给它的发行者。

Sticky就是基于cookie的一种负载均衡解决方案，通过cookie实现客户端与后端服务器的会话保持, 在一定条件下可以保证同一个客户端访问的都是同一个后端服务器。请求来了，服务器发个cookie，并说：下次来带上，直接来找我。

为方便叙述，文中的cookie都指sticky使用的cookie。

#Sticky工作原理
Sticky是nginx的一个模块,通过分发和识别cookie，来使同一个客户端的请求落在同一台服务器上。sticky的处理过程如下（假设cookie名称为route）：

1.客户端首次发起请求，请求头未带route的cookie。nginx接收请求，发现请求头没有route，则以轮询方式将请求分配给后端服务器。

2.后端服务器处理完请求，将响应头和内容返回给nginx。

3.nginx生成route的cookie，返回给客户端。route的值与后端服务器对应，可能是明文，也可能是md5、sha1等Hash值。

4.客户端接收请求，并创建route的cookie。

5.客户端再次发送请求时，带上route。

6.nginx接收到route，直接转给对应的后端服务器。

关于sticky的详细的配置过程在
[这里](http://nginx.org/en/docs/http/ngx_http_upstream_module.html#sticky "nginx sticky详细配置")。

#参数解析

这里引用淘宝Tengine的文档：


    语法：session_sticky [cookie=name] [domain=your_domain] [path=your_path] [maxage=time] [mode=insert|rewrite|prefix] [option=indirect] [maxidle=time] [maxlife=time] [fallback=on|off] [hash=plain|md5]

默认值：session_sticky cookie=route mode=insert fallback=on

上下文：upstream

说明:

本指令可以打开会话保持的功能，下面是具体的参数：

cookie设置用来记录会话的cookie名称

domain设置cookie作用的域名，默认不设置

path设置cookie作用的URL路径，默认不设置

maxage设置cookie的生存期，默认不设置，即为session cookie，浏览器关闭即失效

mode设置cookie的模式:       

insert: 在回复中本模块通过Set-Cookie头直接插入相应名称的cookie。

prefix: 不会生成新的cookie，但会在响应的cookie值前面加上特定的前缀，当浏览器带着这个有特定标识的cookie再次请求时，模块在传给后端服务前先删除加入的前缀，后端服务拿到的还是原来的cookie值，这些动作对后端透明。如："Cookie: NAME=SRV~VALUE"。

rewrite: 使用服务端标识覆盖后端设置的用于session sticky的cookie。如果后端服务在响应头中没有设置该cookie，则认为该请求不需要进行session sticky，使用这种模式，后端服务可以控制哪些请求需要sesstion sticky，哪些请求不需要。

option 设置用于session sticky的cookie的选项，可设置成indirect或direct。indirect不会将session sticky的cookie传送给后端服务，该cookie对后端应用完全透明。direct则与indirect相反。

maxidle设置session cookie的最长空闲的超时时间

maxlife设置session cookie的最长生存期 

fallback设置是否重试其他机器，当sticky的后端机器挂了以后，是否需要尝试其他机器

hash 设置cookie中server标识是用明文还是使用md5值，默认使用md5

maxage是cookie的生存期。不设置时，浏览器或App关闭后就失效。下次启动时，又会随机分配后端服务器。所以如果希望该客户端的请求长期落在同一台后端服务器上，可以设置maxage。
hash不论是明文还是hash值，都有固定的数目。因为hash是server的标识，所以有多少个server，就有等同数量的hash值。

#一些例外
##同一客户端的请求，有可能落在不同的后端服务器上
如果客户端启动时同时发起多个请求。由于这些请求都没带cookie，所以服务器会随机选择后端服务器，返回不同的cookie。当这些请求中的最后一个请求返回时，客户端的cookie才会稳定下来，值以最后返回的cookie为准。

##cookie不一定生效
由于cookie最初由服务器端下发，如果客户端禁用cookie，则cookie不会生效。

##客户端可能不带cookie
Android客户端发送请求时，一般不会带上所有的cookie，需要明确指定哪些cookie会带上。如果希望用sticky做负载均衡，请对Android开发说加上cookie。

#注意事项
* cookie名称不要和业务使用的cookie重名。Sticky默认的cookie名称是route，可以改成任何值。但切记，不可以与业务中使用的cookie重名。
* 客户端发的第一个请求是不带cookie的。服务器下发的cookie，在客户端下一次请求时才能生效。