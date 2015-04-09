---
layout: post
title: 深夜中消失的广告位
---
旧文重发。这篇博客记录了Po主在[腾讯微博](http://t.qq.com "腾讯微博")时的解决的一个Bug，不是大问题，但追查的过程很有意思。也以此纪念一下。

# 诡秘的空白广告位 #
一碗水，放置一晚上，会发生什么呢？

会蒸发，会流失，可能会被小猫喝掉，或被不注意的人踢翻。

把一个页面打开，放置一晚上，会出现什么变化呢？

拿腾讯微博首页来说，每隔30秒去查看计数，隔1分钟去拉取新消息，隔3分钟去看每日任务完成了没。

这些都是正常的请求，但为什么好好的广告会消失了呢。

初始状态，正常显示广告。

![normal ad](http://spetacular.github.io/images/2015-04-10/normal-ad.png)

放置一个晚上后，广告消失，剩下空白！
 
![bad ad](http://spetacular.github.io/images/2015-04-10/bad-ad.png)

# 找出真凶 #
广告位原先存在，后来消失，一定是有什么改动了dom结构。

经过观察，发现广告位消失的重现规则是这样的：

1.	页面加载时，正常
2.	白天放置一段时间，正常
3.	放置一个晚上，异常。

由1和2知道，可能存在某种超时或定时操作，改变了dom结构；由3知道，这个时机发生在晚上。

于是我设想，在一个整晚，页面肯定通过超时或定时请求了某个js文件，而这个js文件会更改dom结构。

# 抓包 #
只知道发生在晚上，但具体什么时候不清楚，所以我尝试用fiddler抓包。

下班前，我打开腾讯微博首页和fiddler，看着一行行记录，我知道，第二天肯定会有些异常出现。

第二天，打开电脑，先查看腾讯微博首页。果然，广告位空白了。

我把fiddler的url记录导出成一个txt文件。有几千条记录，于是写了个小程序，把url分下类。

	<?php
	$handle = @fopen("path/to/urls.txt", "r");
	$urls = array();
	if ($handle) {
	    while (!feof($handle)) {
	        $buffer = fgets($handle, 4096);		
			$url = getUrl($buffer);
			if(in_array($url,$urls) || empty($url)){
				continue;
			}else{
				array_push($urls,$url);
			}
	    }
	    fclose($handle);
	}
	function getUrl($fullurl){
		$array = parse_url($fullurl);
		if(isset($array['host']) && isset($array['path'])){		
			return $array['host'].$array['path'];
		}else{
			return '';
		}
	}
	
	foreach($urls as $url){
		echo $url."\n";
	}
	?>

结果如下：

	---------- PHP ----------
	pyhelp.qq.com/cgi-bin/getloc.so__
	message.t.qq.com/newMsgCount.php
	t.web2.qq.com/channel/poll
	api.t.qq.com/side/get.json
	clients2.google.com/service/update2/crx
	www.gstatic.com/chrome/crlset/1444/crl-set-10054177008346624430.crx.data__
	qos.report.qq.com/collect
	www.google.com/dl/chrome/components/recovery/recovery/win/101.3.21.140/install.crx__
	**api.t.qq.com/asyn/mysidebar_n_new.php**
	www.gstatic.com/chrome/crlset/1445/crl-set-14121586793162109453.crx.data__
	
	Output completed (0 sec consumed) - Normal Termination

*号标记的url恰好是控制广告出现的文件。看了下请求时间，00：01。

这个结果，印证了以前的判断。
 
![json result](http://spetacular.github.io/images/2015-04-10/json-result.png)

# 原因分析 #
mysidebar_n_new.php是一个JSONP，获取数据后将值传递给回调函数mySidebar，该函数负责将数据中的dom结构加载到相应的位置。


广告位是app_ad和app_ad3_1。这里广告的逻辑是这样的，腾讯微博这边只输出dom结构，然后引用广告平台的一段js来填入内容。


而这里的app_ad和app_ad3_1，不管页面逻辑如下，强行将dom结构替换，替换的同时没有去请求广告平台的js，导致有位置没内容，就出现了空白。

# 解决方案 #
确定了原因是晚上0点加载了mysidebar_n_new.php但没有进行相应处理，那么可能的解决方案有以下几种：

- 如果非必要，可以不加载吗？

    不可。跟开发该功能的同事确认，是为了解决特定问题而加载的。


- 能否不进行ajax方式更新，而改为reload？

    不可。如果用户0点正在看，强制刷新会影响用户体验


- 更改mysidebar_n_new.php

    因为页面加载时也会请求这个文件，要改的话，需要判断加载的时间。不是一个优雅的解决方式。


- js在加载mysidebar_n_new.php时进行相应处理

    这种方式较好，解铃还需系铃人。

# 总结 #
这件事情很简单，就是晚上0点加载了一个文件，但没有进行相应处理，引起了广告位空白。
这使我想起一段话：


> “蛮多复杂的事情呢，他又蛮简单，就象你在新兵连学的立正、稍息，那是最标准的。”
> 
> “但是蛮多简单的事情后面呢，又蛮复杂，就象我刚才跟你一说，你连立正都找不到怎么回事了。”


这段话来自《士兵突击》团长王庆瑞。
