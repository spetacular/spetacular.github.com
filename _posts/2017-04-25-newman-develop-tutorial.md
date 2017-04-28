---
layout: post
title: Postman 及 Newman 使用开发指南（五）：Newman 简介
category : nodejs
tagline: "Supporting tagline"
tags : [postman,newman,nodejs]
---

[Newman](https://www.npmjs.com/package/newman) 是 Postman 推出的一个 nodejs 库。利用 Newman，我们可以很方便地运行和测试集合，并用之构造自动化测试和持续集成。

# 安装运行

可以使用 npm 来 newman。如果还未安装 npm，可以 [点此安装](https://nodejs.org/en/download/) 。

```
npm install newman --global;
```

上一节中，我们将 Echo 集合导出，命名为 postman_echo.postman_collection.json。

在命令行运行如下命令即可，即可启动测试。

```
newman run postman_echo.postman_collection.json
```

运行界面如下：

![newman run动画](/images/2017/postman_newman_run.gif)



# 编程实现

新建文件 `test_echo.js`，内容如下：

```
var newman = require('newman'); // require newman in your project

// call newman.run to pass `options` object and wait for callback
newman.run({
    collection: require('./postman_echo.postman_collection.json'),
    reporters: 'cli'
}, function (err) {
	if (err) { throw err; }
    console.log('collection run complete!');
});
```

然后在命令行运行如下命令：

```
node test_echo.js
```

执行效果和直接运行 newman 完全一致。



# 结果导出方式

Postman 提供了5种导出方法：

1. cli：命令行
2. html：网页
3. json
4. junit：以 xml 格式导出
5. teamcity：导出到持续集成（Continuous Integration）工具 [TeamCity](https://www.jetbrains.com/teamcity/)



另外 Postman 的 html 导出方式支持自定义导出模板。如果不指定模板，将采用 [默认模板](https://github.com/postmanlabs/newman/blob/develop/lib/reporters/html/template-default.hbs)。

# API参考

Newman 的核心方法是 `newman.run(options: *object* , callback: *function*) => run: EventEmitter`。其中：

* options 为Newman运行时的设置项。
* callback 为回调函数，会回传 error 和 summary 信息。
* run 为函数主体，可监听事件。

## options 设置项

| 参数                          | 是否必需 | 类型                                       | 解释                                       |
| --------------------------- | ---- | :--------------------------------------- | ---------------------------------------- |
| options                     | 是    | object                                   | 包含 run 一个集合所需的所有信息。                      |
| options.collection          | 是    | object\|string\|[PostmanCollection](https://github.com/postmanlabs/postman-collection/wiki#Collection) | 指定 collection。可以传递collection 对象，也可以传递 URL 或本地  JSON 文件路径。 |
| options.environment         | 否    | object\|string                           | 指定环境变量。可以传递“键=>值“对的对象，也可以传递 URL 或本地文件路径。 |
| options.globals             | 否    | object\|string                           | 指定全局变量。可以传递对象，URL 或本地文件路径。               |
| options.iterationCount      | 否    | number，默认值为1                             | 指定循环的次数。                                 |
| options.iterationData       | 否    | string                                   | 当循环多次时，指定作为数据源的 JSON、CSV、URL的路径。         |
| options.folder              | 否    | string                                   | 集合中文件夹的名称或 ID。设置此项后，只有该文件夹才会执行。          |
| options.timeoutRequest      | 否    | number，默认值无穷大                            | 请求超时时间，以毫秒为单位。默认不超时。                     |
| options.delayRequest        | 否    | number，默认值为 0                            | 请求之间的时间间隔，以毫秒为单位。                        |
| options.ignoreRedirects     | 否    | boolean，默认值为 false                       | 是否允许 3xx 跳转。                             |
| options.insecure            | 否    | boolean，默认值为 false                       | 是否禁止 SSL 验证和允许自签名证书。                     |
| options.bail                | 否    | boolean，默认值为 false                       | 遇到错误时是否终止运行。                             |
| options.suppressExitCode    | 否    | boolean，默认值为 false                       | 是否覆盖退出码（Exit Code）。                      |
| options.reporters           | 否    | string\|array                            | 指定 reporter。可选 cli、json、html 和 junit。指定一种report时，用 string 即可，如  `cli`；指定多种时，用array，如`['cli','html']。` |
| options.reporter            | 否    | object                                   | 指定特定 reporter 的属性。例如 `reporter : { junit : { export : './xmlResults.xml' } }` 或` reporter : { html : { export : './htmlResults.html', template: './customTemplate.hbs' } }`。 |
| options.color               | 否    | boolean                                  | 强制在 CLI 里使用彩色输出。                         |
| options.noColor             | 否    | boolean                                  | 强制在 CLI 里禁止彩色输出。                         |
| options.sslClientCert       | 否    | string                                   | public client certificate file 路径        |
| options.sslClientKey        | 否    | string                                   | private client key file 路径               |
| options.sslClientPassphrase | 否    | string                                   | secret client key passphrase             |
| callback                    | 是    | function                                 | run 完成之后，会调用 callback 函数，会回传 error 和 summary 信息。 |

## callback 回调

| 参数                     | 类型               | 解释                       |
| ---------------------- | ---------------- | ------------------------ |
| error                  | object           | 致命错误信息，如 Newman 无法处理的异常。 |
| summary                | object           | 本次 run 的所有汇总信息           |
| summary.error          | object           | 错误信息                     |
| summary.collection     | object           | 集合信息                     |
| summary.environment    | object           | 环境变量                     |
| summary.globals        | object           | 全局变量                     |
| summary.run            | object           | 本次 run 的信息               |
| summary.run.stats      | object           | 统计信息                     |
| summary.run.failures   | array.\<object\> | 失败信息                     |
| summary.run.executions | array.\<object\> | 每个请求的信息                  |

## run 事件



| 事件               | 描述                              |
| ---------------- | ------------------------------- |
| start            | 开始run collection                |
| beforeIteration  | 一次循环开始之前                        |
| beforeItem       | 一个条目开始处理之前                      |
| beforePrerequest | pre-request 脚本开始执行之前            |
| prerequest       | pre-request 脚本执行完毕              |
| beforeRequest    | HTTP请求发送之前                      |
| request          | HTTP响应接收之后                      |
| beforeTest       | 测试脚本执行之前                        |
| test             | 测试脚本执行之后                        |
| beforeScript     | 任意脚本（测试脚本或pre-request 脚本）开始执行之前 |
| script           | 任意脚本（测试脚本或pre-request 脚本）执行之后   |
| item             | 一个条目执行完毕之后                      |
| iteration        | 一个循环执行完毕之后                      |
| assertion        | 测试脚本里每个测试用例被执行之后                |
| console          | console方法被调用时触发                 |
| exception        | 脚本错误出现时触发                       |
| beforeDone       | run结束之前                         |
| done             | run结束，无论有没有错误都触发                |


完整参考请查看 [https://github.com/postmanlabs/newman](https://github.com/postmanlabs/newman) 。