---
layout: post
title: 每周学习第 1 期：贝叶斯定理、拼写纠正、生日悖论
category : 数学
tagline: "Supporting tagline"
tags : [数学,go,算法]
---
本周天气非常好。
![african-blue-sky](/images/2018/african-blue.jpg)

# 一、贝叶斯定理
贝叶斯定理解决的是，在事件B已经发生的情况下，事件A发生的概率。
![文氏图解决贝叶斯](/images/2018/conditional-probability.jpg)

最重要的公式有两个:

贝叶斯公式 
![贝叶斯公式](/images/2018/a-join-b.png)

全概率公式

![全概率公式](/images/2018/full-probability.png)

直观的解释就是，无论是事件B发生情况下，事件A发生，或者事件A发生情况下，事件B发生，其结果都是事件A和事件B同时发生了。反映到文氏图上，就是 A∩B 。

# 二、拼写纠正
拼写纠正是一个很常见的功能，比如把 config 拼写成 conf，软件提示输错了，并推荐正确写法。
```
git conf
git: 'conf' is not a git command. See 'git --help'.

Did you mean this?
	config
```    

此类问题有很多解决方法，贝叶斯是其中之一。贝叶斯方法的思路是，在输入conf的情况下，计算正确值是config或其它单词的概率是多少，并找出概率最大的单词。

其中的数学原理并不能，但是要解决两个问题：

1.寻找备选项。如果conf可能是错误的，那么如何找到与conf类似的词如confs、config、confidence等  
2.如何确认备选项的概率

对于问题1，简单的方法是通过编辑距离找出相近的单词，如增加、减少、替换、更换位置等，找到所有的备选项。

对于问题2，简单的方法是找一本英文书，分离出所有的单词，计算每个单词出现的次数。

# 三、生日悖论
在23人中，有2人生日相同的概率大于50%；若有50人，则概率增至97。这可能与人们的直觉相悖，因为一年有365天，两人同天生日，通常就是人们常说的“太巧了”。

但经过[计算](https://zh.wikipedia.org/wiki/%E7%94%9F%E6%97%A5%E5%95%8F%E9%A1%8C#%E6%A6%82%E7%8E%87%E4%BC%B0%E8%AE%A1)，发现其概率要高得多。
![](/images/2018/birthday-probability.svg)


我写了一段代码来模拟一下，从1到365的数字中，随机取出2个数，算出2个数相同的概率。

```
package main
import "fmt"
import "time"
import "math/rand"

//return a random number of 1~365
func generateBirth() int{
	rand.Seed(time.Now().UnixNano())
	return 1+rand.Intn(364)
}

//check if at least 2 persons have same birthday
func checkSameBirthDay(num int) bool{
	brithDays := make(map[int]int)
	for i:=0;i<num;i++{
		day := generateBirth()
		brithDays[day]++
		if(brithDays[day] >1){
			return true
		}
	}
	return false
}

func main(){
	personNum := 50
	loopNum := 10000
	sameNum := 0
	for i:=0;i<loopNum;i++{
		if true == checkSameBirthDay(personNum){
			sameNum++
		}
	}
	fmt.Printf("The probability of same birthday in %d persons is %f\n",personNum,float64(sameNum)/float64(loopNum))
}
```

当人数为50人，结果如下：

```
go run birthday.go
The probability of same birthday in 50 persons is 0.973000
```

与计算的值相差无几。
# 四、参考链接
[贝叶斯推断及其互联网应用（一）：定理简介](http://www.ruanyifeng.com/blog/2011/08/bayesian_inference_part_one.html)  

[哈希碰撞与生日攻击](http://www.ruanyifeng.com/blog/2018/09/hash-collision-and-birthday-attack.html)

[维基百科：生日问题](https://zh.wikipedia.org/wiki/%E7%94%9F%E6%97%A5%E5%95%8F%E9%A1%8C)

[
朴素贝叶斯案例2：拼写纠错（python实现）](https://blog.csdn.net/PbGc396Dwxjb77F2je/article/details/78786980)

