---
layout: post
title: 利用正向最大匹配法分词解决自动提取标签问题
---

写文章时，会有自动提取标签的需求；写新闻时，会有查找主题或关键字的需求。如下图，就是分析新闻页面的内容，匹配相关车型。

![自动提取标签匹配](http://spetacular.github.io/images/2015-05-05/example.png)

图1

解决的方法很多，最常见的是基于字典的中文分词。

为了解决自动提取标签的问题，我们需要以下几个步骤：

1.制作字典。字典保存着奥迪、奔驰、宝马等车型信息

2.分析新闻的正文，查看是否有字典中的词。如果有，就提取出来。

中文分词也有很多种方法，基于词典的算法有最大匹配、最小匹配、逐词匹配和最佳匹配，高大上的NLP算法有隐马尔科夫。这里采用最大匹配法。

最大匹配法，顾名思义，就是先匹配最长的词，再匹配较短的词。例如图1，假如字典中有“奥迪Q7”和“奥迪”两个词，最大匹配法会优先匹配“奥迪Q7”，而不会先匹配“奥迪”。


全流程如下图所示：

![中文分词最大匹配法流程图](http://spetacular.github.io/images/2015-05-05/max.png)

图2

## 代码 ##
这里定义MaxWordSegmentation类，其中的run方法返回匹配到的结果。

	class MaxWordSegmentation{

		private $dict = array();//保存字典的list
	
		function __construct($pathToDict){
			$this->dict = $this->loadDict($pathToDict);
		}
		
		//读入词典文件到内存
		function loadDict($pathToDict){
			if(!file_exists($pathToDict)){
				die('Can not find dict file!');
			}
			$dicts = array();
			$handle = @fopen($pathToDict, "r");
			if ($handle) {
				while (!feof($handle)) {
					$word = fgets($handle, 4096);
					//这里的词需要用trim去掉换行符
					$word = trim($word);
					$dicts[$word] = 1;
				}
				fclose($handle);
			}
			return $dicts;
		}
	
		//查看词是否在字典中
		function inDict($word){
			return array_key_exists($word,$this->dict);
		}
	
		//按照词典进行分词。正向最大匹配法
		function run($text,$encode = 'utf-8'){
			$minLen = 0;
			$maxLen = 0;
			//找出最长的单词长度及最短的单词长度
			foreach($this->dict as $key=>$value){
				$iLen = mb_strlen($key,$encode);
				if($minLen > $iLen || $minLen == 0 ){
					$minLen = $iLen;
				}
	
				if($maxLen < $iLen){
					$maxLen = $iLen;
				}
			}
			
	
			$sLen = mb_strlen($text, $encode);
			$result = array();
			for($start = 0;$start < $sLen;$start ++){//外层正文循环	
				for($maxLoop = $maxLen;$maxLoop >= $minLen;$maxLoop --){//内层字典循环
					$word = mb_substr ($text , $start, $maxLoop , $encode);
					//是否匹配成功
					if($this->inDict($word,$this->dict)){//字典查找
						//添加到输出列表
						if(!in_array($word,$result)){
							$result[] = $word;
						}
						break;
					}
				}
				
				
			}
			return $result;
		}
	}

使用示例：

	$text = '日前，奥迪全新SQ7运动版车型的无伪装谍照出人意料的被曝光出来，该车预计今年底或者明年初发布。可以看到，新车已经有一些专属设计明显的区别于普通版的车型。';
	$file = './dict.txt';	
	$obj = new MaxWordSegmentation($file);
	$ret = $obj->run($text);
	var_dump($ret);
	/*
	结果如下：
		array(1) {
		  [0]=>
		  string(15) "奥迪全新SQ7"
		}
	*/


## 改进之处 ##
由于提取标签时，标签通常不会只有一个字，比如“奥迪”、“奔驰”、“宝马”等，子串长度都为2。如果字典中最短的词长为S，那么对对剩下的字符串重新进行匹配处理，不必等到剩余字串的长度为零才结束，只要匹配到S个字符就行。

## 算法分析 ##
假设字典中最短的词长为S，最长的词长为L，正文的长度为N。

外层正文循环：N

内层字典循环：L-S

内层的inDict字典查找最关键，这里采用数组模拟hashtable实现了个O(1)的实现。

所以总的循环次数是(L-S)*N，而(L-S)是个常数，所以时间复杂度是O(N)

## 代码下载 ##

[点此下载](http://spetacular.github.io/html/maxword.zip "点此下载")

