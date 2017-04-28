---
layout: post
title: Postman 及 Newman 使用开发指南（四）：Postman Collection 与 Runner
category : nodejs
tagline: "Supporting tagline"
tags : [postman,newman,nodejs]
---

在 Postman 里，集合（Collection）是一个很重要的概念，它可以将多个请求进行分组。在每个集合内部，又可向下细分为不同的文件夹。结构化的优点是利于后续查看和维护。

# 集合 Collection

## 创建

创建集合有两种方法：

1. 在左边栏处点击新建。

![postman创建集合](/images/2017/postman_collection.jpeg)

2. 保存或另存请求时，直接新建。

   ![postman创建集合](/images/2017/postman_collection_add.jpeg)

## 管理

点击每个集合右侧的“<”和“...”，会分别展开集合详情和集合操作页面。

![postman创建管理](/images/2017/postman_collection_detail.jpeg)

集合详情介绍该集合的名称、简介（支持 Markdown 语法），并可分享、编辑、复制、导出、删除等。点击蓝色的 “Run” 按钮，会进入 Runner 页面。

集合操作则列出对集合的常用操作：分享、重命名、编辑、新增文件夹、复制、导出、删除。Pro 版还有监控和发布文档功能。

## 导出功能

导出功能是一个非常重要的功能。

![postman集合导出](/images/2017/postman_export.jpeg)

导出文件后，你可以分享给同事，在其它电脑上导入，而且可以在命令行中使用。推荐使用v2版本。

# Runner

Runner 是一个对集合中请求进行测试的工具。它会检测每个请求的测试用例，最终返回结果汇总和统计数据。

有两种方式进入 Runner：

1. 集合详情页，点击蓝色的 “Run” 按钮。
2. Postman 左上角 “Runner” 按钮。

进入后，可以选择一个集合，或集合中的某一文件夹进行测试。

![postman Runner](/images/2017/postman_runner_ui.jpeg)

在 Start Run 之前，可以选择环境，确定循环次数，确定发送请求的时间间隔，日志保存选项，并可以上传相关的文件。

点击 Start Run，Runner工具开始按照设置运行。

![postman Runner正在运行](/images/2017/postman_runner_running.jpeg)

全部完成后，展示测试数据。

![postman runner 结果汇总](/images/2017/postman_runner_done.jpeg)

在此页面，可以查看汇总信息，导出结果，重试该测试。

Runner提供了批量测试的功能，Run 一下，就知道接口响应是否符合预期。更重要的是，Runner 可以在命令行运行，即可编程。借助此工具，我们就可以完成定时脚本、健康检测、冒烟测试等功能。下节，我将介绍 ”Run in Command Line“ 的工具：Newman 。