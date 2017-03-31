---
layout: post
title: 小程序新能力：公众号自定义菜单点击打开相关小程序
category : 微信
tagline: "Supporting tagline"
tags : [微信,公众号,小程序]
---

微信在2017年3月27日发布了[《小程序新能力》](https://mp.weixin.qq.com/s?__biz=MjM5NDAwMTA2MA==&mid=2695729676&idx=1&sn=2f0279377bcc6b0ea14d30389dfde698&chksm=83d74bc7b4a0c2d197ebad8e77b4e6a3f5c19a6ef91f0602b4a695d2ef0994fd1e6431a5babb&mpshare=1&scene=1&srcid=0327yIBkOfW0f1CEF7Sc1MY2#rd)的重要更新，主要新增了以下功能：  

1. 个人开发者可申请小程序
2. 公众号自定义菜单点击可打开相关小程序
3. 公众号模版消息可打开相关小程序
4. 公众号关联小程序时，可选择给粉丝下发通知
5. 移动App可分享小程序页面
6. 扫描普通链接二维码可打开小程序

本文主要说明实现公众号自定义菜单点击打开相关小程序。

公众号可将已关联的小程序页面放置到自定义菜单中，用户点击后可打开该小程序页面。公众号运营者可在公众平台进行设置，也可以通过自定义菜单接口进行设置。 

# 1.公众平台设置方式
## 1.1关联小程序

当读者使用公众平台设置方式时，首先要关联公众号和小程序。打开公众平台https://mp.weixin.qq.com，打开“设置” --> “公众号设置”，可以看到如下图：

![公众平台公众号设置，关联小程序](/images/2016/mp-add-relative-minapp.png)

点击“添加”按钮，即进入小程序添加页面。

![公众平台公众号设置，关联小程序](/images/2016/mp-add-relative-minapp-2.png)

扫码验证身份后，即可关联小程序。再输入框输入小程序的AppID，点击搜索按钮，即可完成关联。

注意，只能关联公众号主体一致的小程序。就是说，公共号和小程序的主体如果是个人，则必须是同一个人；如果是企业，则必须是同一企业。

公众号关联小程序时，可勾选“关联后给已关注公众号的用户发送通知”，给粉丝下发通知消息，粉丝点击该通知消息可以打开小程序。该消息不占用原有群发条数。

![公众平台公众号设置，关联小程序](/images/2016/mp-add-relative-minapp-3.png)


## 1.2设置小程序自定义菜单 

可以在“功能管理”下“自定义菜单”下编辑，如下图所示：

![公众平台公众号设置，关联小程序](/images/2016/mp-add-relative-minapp-4.png)
 

填写要素有：

1.菜单名称：底部自定义菜单展示的文字；

2.跳转类型：选择“跳转小程序”Tab；

3.小程序路径：填入小程序路径

4.备用网页：旧版兼容选项。旧版微信客户端无法支持小程序时，用户点击菜单时将会打开备用网页。

 

# 2.开发模式自定义菜单创建

在开发模式下，可以通过自定义菜单创建接口来创建小程序的菜单。文档地址为[https://mp.weixin.qq.com/wiki](https://mp.weixin.qq.com/wiki)下的“自定义菜单”页面。

接口地址如下：
```
https://api.weixin.qq.com/cgi-bin/menu/create?access_token=ACCESS_TOKEN
```
请求内容示例如下：
```
{
  "button": [
    {
      "type": "miniprogram",
      "name": "小程序",
      "appid": "wx1dda1f639e823874",
      "url": "http://www.qq.com",
      "pagepath": "page/index"
    }
  ]
}
```
完整程序如下：

```
<?php
define('APPID', '公众号APPID ');//公众号APPID
define('APPSECRET', '公众号APPSECRET ');//公众号APPSECRET
//$menu变量为存放菜单项的json字符串
$menu =
	'{
"button": [
{
"type": "miniprogram",
"name": "小程序",
"appid": "请填写小程序ID",
"url": "http://www.qq.com",
"pagepath": "page/index"
}
]
}';
$url = "https://api.weixin.qq.com/cgi-bin/menu/create?access_token=" . get_token();
$content = curl_post($url, $menu);
$ret = json_decode($content, true);
if ($ret['errcode'] == 0) {//创建成功
	echo 'create menu ok';
} else {//创建失败
	echo 'create menu fail,msg:' . $ret['errmsg'];
}
function curl_post($url, $post_string) {
	$ch = curl_init();
	curl_setopt($ch, CURLOPT_URL, $url);
	curl_setopt($ch, CURLOPT_POSTFIELDS, $post_string);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	$data = curl_exec($ch);
	curl_close($ch);
	return $data;
}
function curl_get($url) {
	$ch = curl_init();
	curl_setopt($ch, CURLOPT_URL, $url);;
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
	curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
	if (!curl_exec($ch)) {
		$data = '';
	} else {
		$data = curl_multi_getcontent($ch);
	}
	curl_close($ch);
	return $data;
}
function get_token() {
	$url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=" . APPID . "&secret=" . APPSECRET;
	$content = curl_get($url);
	$ret = json_decode($content, true);//{"access_token":"ACCESS_TOKEN","expires_in":7200}
	if (array_key_exists('errcode', $ret) && $ret['errcode'] != 0) {
		return false;
	} else {
		return $ret['access_token'];
	}
}
?>
```

# 欢迎加入微信开发者小密圈
>微信开放平台拥有订阅号、服务号、小程序和企业号，吸引了一大批开发者，也形成了独特的生态圈。希望我们能吸收能量，交换信息，自由生长。

PC端点击链接：[微信生态圈小密圈](https://wx.xiaomiquan.com/mweb/views/joingroup/join_group.html?group_id=4221251218&secret=7hrxv8fo5gg7qnjagobezs1g05wsus91&extra=03805215ea752e7e60e44178c4f4f15003ec659aa155aacda4ae846184cea8fe)

微信扫码：
![微信生态圈](http://wx4.sinaimg.cn/large/7ed0a961ly1fdb80fejqmj20k20u8diq.jpg)