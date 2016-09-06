---
title: Restful API中的JSON模板解析－JSONOut库的介绍
layout: post
category : api
tagline: "Supporting tagline"
tags : [restful , api , json]
---

## 项目地址
[Restful API中的JSON模板解析](http://spetacular.github.io/2016/08/01/json-template-to-json.html)

## 为什么需要JSON模板解析

Restful API的两个重要因素是输入和输出，一个好的API要求输入和输出都清晰可见。
输入包括用户提交的方式、参数等，都是可预期的。尽量不要采用数组接收。例如以下的例子，

	$data = $_POST;
	$User->save($data);

这样的坏处一是无法过滤用户非法、多余的参数提交；二是参数不明，后续无法维护。

输出则要求仅提供满足需要的最小的数据集。例如如下的例子，
	
	$user = User::get(1);
	echo $user->toJson();

这样的坏处一是输出的内容不清晰，只有知道数据表的结构才能了解输出的内容；二是可能一不小心把用户表敏感字段（密码，手机号）输出，造成信息泄漏。

## JSONOut库介绍

解决输出的方法，是采用JSON模板解析的方法。例如用户表有id,name,pass,avatar,phone字段，其中只有name,avatar是需要输出的。那么模板可以这样定义：

	{
	  "name": "",
	  "avatar": ""
	}

当输出用户数据时，仅保留name和avatar，其它字段自动过滤掉。

优点是：

* 输出结构一目了然
* 仅保留满足需要的最小的数据集
* 敏感字段自动过滤，减少信息泄露

算法代码如下：

    class JSONOut
	{
		/**
		*按照JSON模板输出JSON数据
		*@param data 源数据
		*@param tplContent JSON模板内容
		*@param multi 是否为多条内容，默认单条
		*@return string
		*/
		public static function toJSON($data, $tplContent,$multi = false){
	        if(empty($data)){
	            return array();
	        }
	     
	        $tplData = json_decode($tplContent,true);
	        if(!$tplData){
	            return false;
	        }

	        $array = array();
	        if (!$multi) {
	            // 一维数组
	            $array = self::array_intersect_key_recursive($data,$tplData);
	        } else {
	            // 多维数组
	            foreach($data as $key => $value){
	                $array[$key] = self::array_intersect_key_recursive($value,$tplData);
	            }
	        }

	        return json_encode($array);
	    }


	    private static function array_intersect_key_recursive(array $array1, array $array2) {
	        $array1 = array_intersect_key($array1, $array2);

	        foreach ($array1 as $key => &$value) {
	            if (is_array($value) && is_array($array2[$key])) {
	                $value = $this->array_intersect_key_recursive($value, $array2[$key]);
	            }
	        }
	        return $array1;
	    }
	}


## 测试代码

测试代码如下：

	$tplContent = <<<EOT
	{
	  "name": "",
	  "avatar": ""
	}
	EOT;

	//单条数据
	$user  = array('id'=>1,'name' => 'david','pass' => '123456','phone'=>'13888888888','avatar' => 'http://spetacular.github.io/images/favicon.ico');

	echo JSONOut::toJSON($user,$tplContent,false);

	echo "\n";

	//多条数据
	$users  = [array('id'=>1,'name' => 'david','pass' => '123456','phone'=>'13888888888','avatar' => 'http://spetacular.github.io/images/favicon.ico'),
	array('id'=>2,'name' => 'john','pass' => '654321','phone'=>'13888888889','avatar' => 'http://spetacular.github.io/images/favicon.png')];

	echo JSONOut::toJSON($users,$tplContent,true);

测试结果如下：

单条数据：

	{
	  "name": "david",
	  "avatar": "http://spetacular.github.io/images/favicon.ico"
	}


多条数据：

	[
	  {
	    "name": "david",
	    "avatar": "http://spetacular.github.io/images/favicon.ico"
	  },
	  {
	    "name": "john",
	    "avatar": "http://spetacular.github.io/images/favicon.png"
	  }
	]


##未来扩展
注意到JSON模板仅有key，没有value，未来可以在value上扩展，比如默认值，比如：
	
	{"gender":0}

如果数据里没有gender的数据，则默认为0；

或者在value上加过滤函数，比如：

	{"age":"int"}

表示age要转化为整型数字。