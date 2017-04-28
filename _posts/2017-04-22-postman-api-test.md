---
layout: post
title: Postman 及 Newman 使用开发指南（三）：Postman Test 与接口测试
category : nodejs
tagline: "Supporting tagline"
tags : [postman,newman,nodejs]
---

API 开发、测试、维护的过程中，我们希望接口各司其职，并且一贯如此。做到这一点，我们需要检查下列选项：

* 网络连接是否正常
* 响应时间是否控制在1秒以下
* HTTP 返回状态码是否为200
* 符合一定的JSON或XML格式
* 返回内容是否包含特定字符串

只有所有的选项都符合预期，我们才会认为接口正常工作。

Postman Builder页面提供了接口测试的功能。

![单元测试](/images/2017/postman_tests.jpeg)

书写测试用例的方法是，在tests数组中增加一个元素，key为测试用例的名称，value为表达式。如果执行过后表达式的值为 true ，表明通过测试；反之，未通过测试。

```
tests["Body matches string"] = responseBody.has("string_you_want_to_search");
```

Postman 常见的测试用例如下：

设置环境变量

```
postman.setEnvironmentVariable("key", "value");

```

获取环境变量

```
postman.getEnvironmentVariable("key");

```

设置全局变量

```
postman.setGlobalVariable("key", "value");

```

获取全局变量

```
postman.getGlobalVariable("key"); 

```

返回内容是否包含特定字符串

```
tests["Body matches string"] = responseBody.has("string_you_want_to_search");
```

将 XML 转化为 JSON

```
var jsonObject = xml2Json(responseBody);
```

返回内容是否为特定字符串

```
tests["Body is correct"] = responseBody === "response_body_string";
```

检查 JSON 数据的某个字段是否等于特定值

```
var data = JSON.parse(responseBody);
tests["Your test name"] = data.value === 100;

```

检查是否存在 Content-Type（区分大小写）

```
tests["Content-Type is present"] = postman.getResponseHeader("Content-Type"); //Note: the getResponseHeader() method returns the header value, if it exists.
```

检查是否存在 Content-Type（区分大小写）

```
tests["Content-Type is present"] = responseHeaders.hasOwnProperty("Content-Type");
```

响应时间小于 200ms

```
tests["Response time is less than 200ms"] = responseTime < 200;

```

HTTP 返回状态码为 200

```
tests["Status code is 200"] = responseCode.code === 200;

```

HTTP 返回状态码包含特定字符串

```
tests["Status code name has string"] = responseCode.name.has("Created");
```

POST 请求成功的状态码

```
tests["Successful POST request"] = responseCode.code === 201 || responseCode.code === 202;
```

利用 TinyValidator 来检查 JSON 是否符合特定格式

```
var schema = {
 "items": {
 "type": "boolean"
 }
};
var data1 = [true, false];
var data2 = [true, 123];

tests["Valid Data1"] = tv4.validate(data1, schema);
tests["Valid Data2"] = tv4.validate(data2, schema);
console.log("Validation failed: ", tv4.error);

```

该例子的样本文件如下：

JSON 文件：[Download JSON file](http://www.getpostman.com/samples/test_data_file.json)

CSV 文件：[Download CSV file](http://www.getpostman.com/samples/test_data_file.csv)