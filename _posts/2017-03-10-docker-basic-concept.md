---
layout: post
title: Docker:基本概念
category : docker
tagline: "Supporting tagline"
tags : [docker]
---

# [镜像](#image)
Docker镜像Image是一个就像具有[Time Machine](https://zh.wikipedia.org/wiki/Time_Machine)功能的虚拟机，保存着特定时刻的一个快照。这个快照包含了已安装的程序、共享库、配置文件、环境变量、用户组等信息。构建Image时，以上内容不会改变。这意味着可以利用Image快速复制出多个相同的运行实体（容器）。

# [容器](#container)
Docker容器container是镜像Image的运行实体。一个镜像可以生成多个相同的容器，每个容器的代码、运行环境、系统工具、系统库都完全相同。这在环境部署、服务扩容等方面具有重大作用。

# [仓库](#registry)
Docker仓库Registry是一个镜像存储和分发的服务。一个 Docker Registry中可以包含多个仓库（Repository）；每个仓库可以包含多个标签（Tag）；每个标签对应一个镜像。

# [构建](#build)
利用 [Dockerfile](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/#run) 定制镜像时，可以用`docker build`命令，将指令构建为文件系统。

# [宿主机](#host)
宿主机就是运行Docker容器的机器。例如我在mac上用[Docker for Mac](https://docs.docker.com/docker-for-mac/)，执行`docker pull ubuntu`来拉取一个ubuntu镜像然后运行，那么宿主机指的是mac。