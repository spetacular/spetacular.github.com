---
layout: post
title: Javascript整型数字精度限制
category : js
tagline: "Supporting tagline"
tags : [js]
---
在做一个项目时，需要生成一个长整数Id，生成函数如下：

    `$Id = date('YmdHis') . rand(1, 100000);`

按照时间加随机数的形式，可以直观地看到这个Id是何时生成的。

这样，我用PHP生成了这样一个数

    $id = '2014112010185143423';

然后将值传给JS变量

    var id = <?php echo $id;?>;

但是在使用时，发现id竟然变了。

![精度丢失](http://spetacular.github.io/images/2015-01-20/js_int_lose.png)
 


猜想可能是溢出，把dialogid给换成字符串，一切就OK了。

    var id = '<?php echo $id;?>';

![用字符串处理](http://spetacular.github.io/images/2015-01-20/js_string_int.png)

## 为啥动了我的数？ ##
原来JS采用IEEE 754定义的双精度浮点数。当有效位数超过 52 位时，会存在精度丢失。

IEEE 754表示的64位双精度数据的格式如下，其中52位的Significand是无符号整数。你可以将Significand左移或右移，移动的位数保存在Exponent里，11位可以表示+(2^10 -1) ~ -(2^10 -1)的范围。Sign表示正负。

<table border="1">
    <tr>
        <th>Total</th><th>bits</th><th>Exponent</th><th>Significand</th>
 	</tr>
 	<tr>
		<td>64</td><td>1</td><td>11</td><td>52</td>
    </tr>
</table>


这就是说，当Exponent起作用时，精度会丢失。即：
左移时，数字末位补零；
右移时，数字末位丢失。

回到前面提到的数字Id，数字大于2^53-1，表示该数时，相当于将Significand左移，只能将末位补零。

另外，js的Number提供了最大无误差的数字：

> Number.MAX_SAFE_INTEGER is 9007199254740991 (2^53−1).


参考资料：

[http://blog.vjeux.com/2010/javascript/javascript-max_int-number-limits.html](http://blog.vjeux.com/2010/javascript/javascript-max_int-number-limits.html)

[http://lifesinger.wordpress.com/2011/03/07/js-precision/](http://lifesinger.wordpress.com/2011/03/07/js-precision/)
