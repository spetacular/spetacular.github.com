---
layout: post
title: Docker:网络配置
category : docker
tagline: "Supporting tagline"
tags : [docker]
---

Docker 一个常用用法是开放特定端口来对外提供服务，如 Mysql 占用3306端口，redis 占用 6379 端口。这样就牵扯到网络配置的问题（以 Mysql 为例）：    
1. localhost(127.0.0.1)指向哪里？     
2. [宿主机](/2017/03/10/docker-basic-concept.html#host)如何连接到Mysql服务所在的容器？   
3. 另一台运行在容器内的php服务如何连接到 Mysql 服务器所在的容器？

Docker支持的网络模式如下：bridge（默认）、host、container、network-name、none。

# 桥接 bridge
Docker 默认的网络模式是 bridge 。在该模式下，docker 创建了一个 bridge，名称通常为 `docker0` 。   
可以用`ifconfig`来查看：
```
docker0   Link encap:Ethernet  HWaddr **:**:**:**:**:**
          inet addr:192.168.0.1  Bcast:0.0.0.0  Mask:255.255.240.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:305002 errors:0 dropped:0 overruns:0 frame:0
          TX packets:407006 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:30331811 (30.3 MB)  TX bytes:752905683 (752.9 MB)
```
宿主机和容器通过bridge进行通信，如图所示：

![docker桥接bridge模式](/images/2016/docker-networking-bridge-mode.jpeg)

容器内的localhost指向容器内部。

# 本机 host
当使用 `--net=host`启动容器时，网络配置为 host 模式。该模式下，容器和宿主机共享网络。形象地讲，容器和宿主机共享一个网卡，在容器内的网络访问如同直接在宿主机上操作一样。例如：
```
docker run --rm -it --net=host ubuntu:trusty bash
```
容器内的localhost指向宿主机。
![docker桥接host模式](/images/2016/docker-networking-host-mode.jpeg)

# container
Docker可以指定一个容器复用另一个容器的网络设置。这种模式适用于由多个容器搭建整套系统的情况，例如搭建PHP开发环境时，希望Mysql、Redis都使用PHP容器的网络配置，即可以使用该模式。

# network-name
Docker允许用户使用Docker network driver或[第三方network driver插件]（https://docs.docker.com/engine/extend/legacy_plugins/#network-plugins）创建自定义网络，然后多个容器都可以使用相同的网络。常见的第三方etwork driver插件有：[Contiv Networking](https://github.com/contiv/netplugin)、[Kuryr Network Plugin](https://github.com/openstack/kuryr)、[Weave Network Plugin](https://www.weave.works/docs/net/latest/introducing-weave/)。

# none
顾名思义，该模式下没有网络连接。例如我的宿主机ip为192.168.0.59，在容器内访问宿主机就提示无网络。
```
docker run -it --net=none my_ubuntu bash
root@6533968160dd:/# ping 192.168.0.59
connect: Network is unreachable
```


<!-- # Docker为什么没有 nat 模式 -->