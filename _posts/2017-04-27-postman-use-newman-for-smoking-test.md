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

```javascript
var newman = require('newman'); // require newman in your project 
var nodemailer  = require('nodemailer');
var fs = require('fs');

var export_file = './htmlResults_for_mail.html';
var collection_file = './postman_echo.postman_collection.json';
// call newman.run to pass `options` object and wait for callback 
newman.run({
    collection: require(collection_file),
    reporters: ['cli','html'],
    reporter : { html : { export : export_file,template: './template.hbs'} } 
}, function (err,summary) {
    if (err) { throw err; }
    console.log('collection run complete!');
    console.dir(summary);

    var network_total = summary['run']['stats']['requests']['total'];
    var network_failed = summary['run']['stats']['requests']['failed'];
    var network_success = network_total - network_failed;
    
    var unit_total = summary['run']['stats']['assertions']['total'];
    var unit_failed = summary['run']['stats']['assertions']['failed'];
    var unit_success = unit_total - unit_failed;
    var stats = "网络请求"+network_total+"次，成功"+network_success+"次，失败"+network_failed+"次。\n共执行单元测试"+unit_total+"次，成功"+unit_success+"次，失败"+unit_failed+"次";
    
    if(network_failed>0 || unit_failed > 0){
        console.log("Something Is WRONG");
        var tracelog = JSON.stringify(summary['run']['failures'], null, 2);
        send(stats,tracelog);
    }else{
        console.log("Everything Is OK");
    }
});

function send(sub,tracelog){
    var transporter = nodemailer.createTransport({
		service: "QQex|Gmail",
		auth: {
			user: "---发件人邮箱---",
			pass: "---发件人邮箱密码---"
		}
	});
    
    var html = fs.readFileSync(export_file);
    var to = '---收件人地址---';
	var mailOptions = {
		from: "---发件人邮箱地址---",
		to: to,
		subject: "冒烟测试问题报告:"+sub,
        html:html,
        attachments:[{   
                filename:'results.html',
                path: export_file,
                contentType: 'text/html' 
                },
                {   
                    filename: 'tracelog.txt',
                    content: tracelog,
                    contentType: 'text/plain'
                }
        ],
	};

	transporter.sendMail(mailOptions, function(error, info) {
		if (error) {
			throw error;
		} else {
			fn();
		}
	});
}
```

# 自定义 HTML 模板

由于大部分邮件客户端只支持 inline 的 CSS 样式，所以我们将样式写到元素里。

template.hbs 文件：

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Smoke Test Report</title>
</head>
<body>
  <div class="container">
      {{#if summary.failures}}<h1 style="color:red;">SomeThing Is Wrong! Check The Following Contents and Attachments To Find Out!</h1><hr/>{{/if}}
    <h3>Smoke Test Report</h3>  
    
    {{#with summary}}    
        <p>Collection:   {{collection.name}}</p>
        <p>Desc:     {{collection.description}}</p>
        
    {{/with}}
    <p>Exec At:  {{timestamp}}</p>
    <p>Version:  Newman v{{version}}</p>

    {{#with summary}}
        {{#with stats}}
        <table style="margin-top: 10px;border-collapse: collapse; border: 1px solid #aaa;width: 50%;"><tbody>
            <tr><th style="vertical-align: baseline;padding: 5px 15px 5px 6px;background-color: #d5d5d5;border: 1px solid #aaa;text-align: left;">Item</th><th style="vertical-align: baseline;padding: 5px 15px 5px 6px;background-color: #d5d5d5;border: 1px solid #aaa;text-align: left;">Total</th><th style="vertical-align: baseline;padding: 5px 15px 5px 6px;background-color: #d5d5d5;border: 1px solid #aaa;text-align: left;">Failed</th></tr>
            
            <tr><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">Iterations</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{iterations.total}}</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{iterations.failed}}</td></tr>
            <tr><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">Requests</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{requests.total}}</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{requests.failed}}</td></tr>
            <tr><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">Prerequest Scripts</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{prerequestScripts.total}}</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{prerequestScripts.failed}}</td></tr>
            <tr><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">Test Scripts</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{testScripts.total}}</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{testScripts.failed}}</td></tr>
            <tr><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">Assertions</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{assertions.total}}</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;"><span style="color:red;">{{assertions.failed}}</span></td></tr>
  
        </tbody></table>
        {{/with}}
        <br/>
        <table style="margin-top: 10px;border-collapse: collapse; border: 1px solid #aaa;width: 50%;"><tbody>
            <tr><th style="vertical-align: baseline;padding: 5px 15px 5px 6px;background-color: #d5d5d5;border: 1px solid #aaa;text-align: left;">Stats</th><th style="vertical-align: baseline;padding: 5px 15px 5px 6px;background-color: #d5d5d5;border: 1px solid #aaa;text-align: left;">Value</th></tr>
            <tr><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">Total run duration</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{duration}}</td></tr>
            <tr><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">Total data received</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{responseTotal}} (approx)</td></tr>
            <tr><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">Average response time</td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;">{{responseAverage}}</td></tr>
            <tr><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;"><strong>Total Failures</strong></td><td style="vertical-align: text-top;padding: 6px 15px 6px 6px;background-color: #efefef;border: 1px solid #aaa;"><span style="color:red;">{{failures}}</span></td></tr>
        </tbody></table>
      {{/with}}

</body>
</html>
```

最后发送邮件的效果如下：

![newman冒烟测试效果](/images/2017/postman_newman_mail.jpeg)



完整代码参考 [https://github.com/spetacular/newman-smoking-test](https://github.com/spetacular/newman-smoking-test)