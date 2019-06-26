---
layout: post
title: Scala 简明教程
category : scala
tagline: "Supporting tagline"
tags : [scala]
---

[TOC]


# Scala 简介

Scala 集成了面向对象编程和函数式编程语言的各种特性。Scala 的静态类型帮助减少复杂系统中的 bug，并且支持 JVM 和 JavaScript 运行环境，同时还提供丰富的类库可供使用。

Scala 的官网地址为 [https://www.scala-lang.org](https://www.scala-lang.org/)

## 安装与配置

Scala 运行环境依赖于 JVM，所以要先安装 Java。之后再安装 Scala，最后安装 sbt。

### 安装 Java

在 oracle 官网下载 JavaSE 1.5 以上的版本。下载地址如下：

[http://www.oracle.com/technetwork/java/javase/downloads/index.html](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

这里我选择 1.8 版本，根据自己的操作系统选择合适的下载包。

![JDk 1.8](/images/2019/jdk_18.png)

设置环境变量的教程在这里：[https://www.runoob.com/java/java-environment-setup.html](https://www.runoob.com/java/java-environment-setup.html)

设置完成后，我们可以在终端查看 `java` 和 `javac` 命令:

```
$ java -version
java version "1.8.0_181"
Java(TM) SE Runtime Environment (build 1.8.0_181-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.181-b13, mixed mode)
$ javac -version
javac 1.8.0_181
```

### 安装 Scala

Scala 的下载地址为 [https://www.scala-lang.org/download/](https://www.scala-lang.org/download/) 。可以根据自己的操作系统选择合适的下载包。

如果操作系统为 Windows，可以查找 msi 的安装文件。

如果操作系统为 Mac，可以使用 Homebrew 来安装：

```
brew update
brew install scala
```

如果操作系统为 Ubuntu，直接用 `apt-get install scala` 来进行安装。

安装完成后，可以运行如下命令来查看 `scala` 命令：

```
$ scala -version
Scala code runner version 2.13.0 -- Copyright 2002-2019, LAMP/EPFL and Lightbend, Inc.
```

### 安装 sbt

sbt 的全称为 Simple Build Tool，用于管理 Scala 项目的依赖并进行构建，其地址就像 Maven 之于 Java。其下载地址为 [https://www.scala-sbt.org/download.html](https://www.scala-sbt.org/download.html)。

## Hello World

我们演示三种输出 `Hello World` 的方式。

### 交互模式

在命令行输入 `scala` 命令，进入交互模式。

```shell
$ scala
Welcome to Scala 2.13.0 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_181).
Type in expressions for evaluation. Or try :help.

scala> println("Hello World");
Hello World
```

### 编译模式

创建一个文件 HelloWorldDemo.scala，文件内容如下：

```scala
object HelloWorldDemo {
   def main(args: Array[String]):Unit = {
      println("Hello World");
   }
}
```

运行编译及运行命令：

```shell
$ scalac HelloWorldDemo.scala
$ scala HelloWorldDemo
Hello World
```

### 构建模式

可以使用 sbt 来构建。如编译模式的文件为例，我们将其放到 helloworld 目录里。确保该目录只有一个文件。输入 sbt 命令，然后用 run 命令运行。

```shell
$ sbt
sbt:helloworld> run
[info] Updating ...
[info] Done updating.
[info] Compiling 1 Scala source to /Users/didi/code/scala/helloworld/target/scala-2.12/classes ...
[info] Non-compiled module 'compiler-bridge_2.12' for Scala 2.12.7. Compiling...
[info]   Compilation completed in 8.672s.
[info] Done compiling.
[info] Packaging /Users/didi/code/scala/helloworld/target/scala-2.12/helloworld_2.12-0.1.0-SNAPSHOT.jar ...
[info] Done packaging.
[info] Running HelloWorldDemo 
Hello World
[success] Total time: 18 s, completed 2019-6-25 15:19:19
sbt:helloworld> run
[info] Running HelloWorldDemo 
Hello World
[success] Total time: 0 s, completed 2019-6-25 15:19:23
```

可以看到，第一次 run 时，需要进行编译，耗时 18 s。第二次 run 时，不需再编译即可运行，耗时可以忽略不计。

# 基本语法

## 基本概念

* 类：外部事物的抽象，具有方法和属性。

* 对象：对象是类的具体实例；类是对象的抽象。
* 方法：方法是类的某个行为动作。一个类可以包含多个方法。
* 属性：类的若干属性，又称为字段。
* 语句：语句是执行代码的单元。如果一行只有一个语句，那么结尾不需要加`;`分号。如果一行之内有多个语句的话，需要加分号。
* 注释：支持多行注释 `/* */` 和单行注释 `//`。
* 包：使用 `package`  定义包，类似于命名空间。
* 引用：使用 `import` 引用包。注意 Scala 的包引用可以出现在任何地方，而不只是文件的顶部。
* 标识符：标识符可以由字母和下划线开头，后面可以连接数字、字母和下划线。区分大小写。
* 类名：类名采用驼峰写法，每个单词首字母大写。
* 方法名：方法名称的第一个字母用小写，其余每个单词的第一个字母大写。
* 程序文件名：程序文件名称和对象名称要完全匹配。虽然新版本不再要求，但为了保证代码能通过旧版本编译器，还是保持一致较好。
* 程序入口：程序入口为 main 方法。

## 数据类型

Scala 的数据类型分为以下几类：

### 整数

| 类型   | 说明                    |
| ----- | ------------------------------------------------------------ |
| Byte  | 8位有符号补码整数。数值区间为 -128 到 127                    |
| Short | 16位有符号补码整数。数值区间为 -32768 到 32767               |
| Int   | 32位有符号补码整数。数值区间为 -2147483648 到 2147483647     |
| Long  | 64位有符号补码整数。数值区间为 -9223372036854775808 到 9223372036854775807 |


### 浮点数

| 类型   | 说明                    |
| ----- | ------------------------------------------------------------ |
| Float  | 32 位, IEEE 754 标准的单精度浮点数 |
| Double | 64 位 IEEE 754 标准的双精度浮点数  |


### 字符

| 类型   | 说明                    |
| ----- | ------------------------------------------------------------ |
| Char   | 16位无符号Unicode字符, 区间值为 U+0000 到 U+FFFF |
| String | 字符序列                                         |


### 布尔

| 类型   | 说明                    |
| ----- | ------------------------------------------------------------ |
| Boolean | true或false |


### 其它类型

| 类型   | 说明                    |
| ----- | ------------------------------------------------------------ |
| Unit    | 表示无值，和其他语言中void等同。用作不返回任何结果的方法的结果类型。Unit只有一个实例值，写成()。 |
| Null    | null 或空引用                                                |
| Nothing | Nothing类型在Scala的类层级的最低端；它是任何其他类型的子类型。 |
| Any     | Any是所有其他类的超类                                        |
| AnyRef  | AnyRef类是Scala里所有引用类(reference class)的基类           |


### 变量与常量

变量在程序运行过程中，其值可能发生改变，采用 `var` 声明变量；

常量在程序运行过程中，其值不会发生改变，采用 `val` 声明常量。

声明变量和常量的语法如下：

```
var VariableName : DataType [=  Initial Value]

或

val VariableName : DataType [=  Initial Value]
```

另外 DataType 是可选的。如果不指定数据类型，编译器会自动根据初始值推断出数据类型。

## 流程控制逻辑

Scala 的流程控制主要包括条件控制和循环控制。

### 条件控制

其使用方法和其它语言类似，示例如下：

```scala
if(布尔表达式 1){
   // 如果布尔表达式 1 为 true 则执行该语句块
}else if(布尔表达式 2){
   // 如果布尔表达式 2 为 true 则执行该语句块
}else if(布尔表达式 3){
   // 如果布尔表达式 3 为 true 则执行该语句块
}else {
   // 如果以上条件都为 false 执行该语句块
}
```

### 循环控制

循环支持三种循环类型：while 循环、do while 循环和 for 循环。

1. while 循环

   ```
   while(condition)
   {
      //语句块
   }
   ```

2. do while循环

   ```
   do {
      //语句块
   } while( condition );
   ```

3. for 循环

   ```
   for( var x <- Range ){
      //语句块
   }
   ```

### 循环控制的 break 和 continue

 Scala 的 break 的语法和其它语言不同，它完全是基于类的一种实现。

文件：BreakDemo.scala

```scala
import scala.util.control.Breaks;
object BreakDemo {
   def main(args: Array[String]) {
      var i = 0;
      val numList = List(1,2,3,4);

      val loop = new Breaks;
      loop.breakable {
         for( i <- numList){
            println( "第" + i + "次" );
            if( i == 3 ){
               loop.break;
            }
         }
      }
      println( "事不过三" );
   }
}
```

输出结果：

```
第1次
第2次
第3次
事不过三
```

遗憾的是，Scala 里并没有 continue 的语法，即跳过本次循环。但想一下 continue 的实质是什么？跳过满足特定条件的循环中的元素，即过滤掉某些元素，让其不参与循环。Scala 提供了循环过滤的机制。

我们看一下示例。

文件：ForFilterDemo.scala

```scala
import java.util.Random;
object ForFilterDemo {
   def main(args: Array[String]) {
        var r = new Random()
        var rand = r.nextInt(100)
        var i = 0
        for(i <- 0 to 10
            if rand % 2 == 0//只保留所有的偶数
        ){
            println(rand)
            rand = r.nextInt(100)
        }
      
   }
}
```

输出结果（每次都不相同）：

```
6
90
12
```

我们循环 10 次，每次取一个 100 以内的随机数，要求只保留偶数，所有的奇数都过滤掉。实现上可以用 for + if 的循环过滤机制来实现。

# 类和对象

Scala 类的特征有如下几个：

* 单继承

* 无 static 静态属性和方法，但提供了单例对象（singleton objects）的概念
* 支持抽象类和抽象方法
* 支持重载（override）



## 一个例子

首先看一个类的示例。Point 类有两个属性 x 和 y，并有一个方法 move。

文件：PointDemo.scala

```scala
import java.io._

class Point(val xc: Int, val yc: Int) {
   var x: Int = xc
   var y: Int = yc
   
   def move(dx: Int, dy: Int) {
      x = x + dx
      y = y + dy
      println ("Point x location : " + x);
      println ("Point y location : " + y);
   }
}

object PointDemo {
   def main(args: Array[String]) {
      val pt = new Point(10, 20);

      // Move to a new location
      pt.move(10, 10);
   }
}
```

运行命令如下：

```shell
> scalac PointDemo.scala
> scala PointDemo
Point x location : 20
Point y location : 30
```

在这个例子里，需要注意的是，如果文件中定义了不止一个类，则要使用 `scalac` 进行编译后再运行。

这个例子还可以学到构造函数和 singleton objects 的知识，我们分述如下。

## 构造函数

Scala 的构造函数分为主构造函数和辅助构造函数。

### 主构造函数

在上例中，定义 Point 类的语句如下：

```
class Point(val xc: Int, val yc: Int) 
```

其中 `xc`、`yc` 为构造函数的参数。那么其函数的定义在哪里呢？

在类的主体里，除了 `def` 定义的函数之外，其余部分都是主构造函数。

### 辅助构造函数

辅助构造函数用于创造不同类型、不同数目参数的构造函数。定义方法如下：

```scala
def this(){
	//function body
}
```

我们改造一下`PointDemo.scala`文件，来演示主构造函数和辅助构造函数。

文件：PointDemo2.scala

```scala
import java.io._

class Point(val xc: Int, val yc: Int) {
   var x: Int = xc
   var y: Int = yc
   
   def this(){//if no params,set (0,0)
       this(0,0)
       println("Auxiliary constructor")
   }
   def move(dx: Int, dy: Int) {
      x = x + dx
      y = y + dy
      println ("Point x location : " + x);
      println ("Point y location : " + y);
   }
   println("Main Constructor");//this is main constructor too
}

object PointDemo2 {
   def main(args: Array[String]) {
      val pt1 = new Point(10, 20);
      // Move to a new location
      pt1.move(10, 10);

      val pt2 = new Point();
      // Move to a new location
      pt2.move(10, 10);
   }
}
```

输出结果：

```shell
Main Constructor
Point x location : 20
Point y location : 30
Main Constructor
Auxiliary constructor
Point x location : 10
Point y location : 10
```

我们看以下问题：

1. 主构造函数和辅助构造函数有什么区别？

答案是没有区别。当我们实例化一个类时，编译器会自动匹配参数类型和参数个数相符的构造的函数。

2. 能否将主构造函数和辅助构造函数的参数完全一致？

答案是不能。我们定义辅助构造函数如下：

```scala
//    demo for same main and auxiliary constructors  
      def this(xc: Int, yc: Int){
          this(xc,yc)
          println("Auxiliary constructor")
      }
```

结果编译通不过。

### singleton objects

`singleton object` 有如下特征：

* `singleton object` 里的方法是全局可见的

* 不能为 `singleton object` 创建实例

-  `singleton object` 的主构造函数不接收参数
- `singleton object` 可以继承 ` class`  和 ` traits`
-  `singleton object`始终存在 `main` 方法
- 不需要创建实例即可调用 `singleton object` 的方法

我们查看一下实例。

文件：SingletonDemo.scala

```scala
object Exampleofsingleton 
{ 
      
    // Varaibles of singleton object 
    var str1 = "Welcome ! MIS"; 
    var str2 = "This is Scala language tutorial"; 
      
    // Method of singleton object 
    def display() 
    { 
        println("Called By Display Method")
        println(str1); 
        println(str2); 
    } 
} 
  
// Singleton object with named as Main 
object SingletonDemo  
{ 
    def main(args: Array[String]) 
    { 
          
        // Calling method of singleton object  
        Exampleofsingleton.display(); 

        println("Called By Property")
        println(Exampleofsingleton.str1);
        println(Exampleofsingleton.str2);
    } 
} 
```

输出结果如下：

```
Called By Display Method
Welcome ! MIS
This is Scala language tutorial
Called By Property
Welcome ! MIS
This is Scala language tutorial
```

## 伴生对象 Companion Object

我们经常会遇到这样的场景：定义一个类，实现常见的功能；再定义一个类来调用以完成特定的任务。这样带来命名问题，这有时也是让开发者头疼的一件事情。

Scala 的伴生对象完美解决了这个问题。我们先看下例子：

文件：SingletonDemo.scala

```scala
// Companion class 
class CompanionDemo 
{ 
      
    // Variables of Companion class 
    var str1 = "MIS"; 
    var str2 = "Tutorial of Companion object"; 
    private var str3 = "Hello MIS"
      
    // Method of Companion class 
    def show() 
    { 
        println(str1); 
        println(str2); 
    } 

    private def mis()
    {
        println("In mis method")
        println(str3)
    }
} 
  
// Companion object 
object CompanionDemo
{ 
    def main(args: Array[String])  
    { 
        var obj = new CompanionDemo(); 
        obj.show(); 
        println(obj.str3);
        obj.mis();
    } 
}   
```

输出结果：

```
MIS
Tutorial of Companion object
Hello MIS
In mis method
Hello MIS
```

可以看到同时存在 `class CompanionDemo`  和 `object CompanionDemo`。

伴生对象的特点如下：

* `companion object ` 和 `companion class` 的名称必须完全一致
* `companion object ` 必须和 `companion class` 定义在同一个文件中
* `companion object `  可以访问  `companion class` 的私有属性和私有方法

伴生对象除了解决命名问题外，还可以对外提供接口，方便调用。

## 继承

Scala 是单继承的语言，子类只有一个父类。

###抽象类和抽象方法

定义一个抽象类，需要使用 `abstract` 关键字。例如：

```scala
abstract class Animal(iname:String)
{
   //class body
   def eat 
}
```

定义抽象方法，执行保持方法的函数体为空即可。

子类继承抽象父类，必须实现父类的所有的抽象方法。

### override

 `override` 关键字有以下特点：

* 子类重写父类的抽象方法，不需要加 `override` 关键字。

* 子类重新父类的普通方法，需要加 `override` 关键字。

* 只有主构造函数才可以往父类的构造函数里写参数。

### 一个例子

我们看一个演示继承的例子。

文件：InheritDemo.scala

```scala
abstract class Animal(iname:String)
{
    val name:String = iname;
    def eat 
    def move(){
        println("All Animals Can Move");
    }
    def display(){
        println("Hello "+name)
    }
}

class Dog(iname:String) extends Animal(iname:String) 
{
    def eat(){
        println("Dog Eats Bones");
    }
    override def move(){
        println("Dog is An Animal, So Dog can move");
    }
    def display(msg:String){
        println("Hello "+name+" "+msg)
    }
}

object InheritDemo
{
    def main(args: Array[String])  
    { 
       val dog = new Dog("Teddy");
       dog.eat();
       dog.move();
       dog.display("Welcome!")
    } 
}
```

输出结果：

```
Dog Eats Bones
Dog is An Animal, So Dog can move
Hello Teddy Welcome!
```

`Dog` 类的 `display` 方法为什么没有加 `override` ？

答案是 `Dog` 类的 `display` 方法的参数和 `Animal` 类的 `display` 方法不同，不属于重写，所以不需要加 `override` 。

# 模式匹配

Scala 使用 `case` 关键字实现多条件逻辑匹配的功能。每个条件是一个备选项，称为模式。每个备选项包含一个模式和一到多个表达式。语法如下：

```
case 模式 => 语句;
```

我们看几个例子，学习模式匹配。

## 简单例子（整型数值匹配、温度判断）

文件：Temperature.scala

```scala
object Temperature {
   def main(args: Array[String]) {
        println(display(37))
        println(display(100))
        println(display(50))
   }
   def display(x: Int): String = x match {
      case 0 => "freezing point" 
      case 100 => "boiling point" 
      case x if(x >= 36.5 && x <= 37.5) => "body point"
      case _ => "other point"
   }
}
```

输出结果：

```
body point
boiling point
other point
```

match 表达式通过以代码编写的先后次序尝试每个模式来完成计算，只要发现有一个匹配的case，剩下的case不会继续匹配。

## case class

文件：CaseClassDemo.scala

```scala
object CaseClassDemo {
   def main(args: Array[String]) {
       val alice = new Person("Alice", 25)
    	 val bob = new Person("Bob", 32)
       val charlie = new Person("Charlie", 32)
   
    for (person <- List(alice, bob, charlie)) {
        person match {
            case Person("Alice", 25) => println("Hi Alice!")
            case Person("Bob", 32) => println("Hi Bob!")
            case Person(name, age) =>
               println("Age: " + age + " year, name: " + name + "?")
         }
      }
   }
   // 样例类
   case class Person(name: String, age: Int)
}
```

输出结果：

```
Hi Alice!
Hi Bob!
Age: 32 year, name: Charlie?
```

在声明样例类时，下面的过程自动发生了：

- 构造器的每个参数都成为val，除非显式被声明为var，但是并不推荐这么做；
- 在伴生对象中提供了apply方法，所以可以不使用new关键字就可构建对象；
- 提供unapply方法使模式匹配可以工作；
- 生成toString、equals、hashCode和copy方法，除非显示给出这些方法的定义。

## apply

将对象以函数的方式进行调用时，scala会隐式地将调用改为在该对象上调用apply方法。例如XXX(“hello”)实际调用的是XXX.apply(“hello”), 因此apply方法又被称为注入方法。apply方法常用于创建类实例的工厂方法。示例如下：

```scala
object Greeting{
  def apply(name: String) = "Hello " + name
}

Greeting.apply(“Lucy”) //与下面的调用等价 
Greeting(“Lucy”) //结果为 Hello Lucy

```

## unapply

与 apply 相对的是 unapply 方法，它的用法与 apply 类似，但其作用是用来抽取部分参数，它也称为抽取方法，主要用于模式匹配时抽取某些参数 case XXX(str) => println(str)

# 并发编程 Akka

从 Scala 的 2.11.0 版本开始，Scala 的 Actors 库已经过时了。早在 Scala 2.10.0 的时候，默认的 actor 库即是 Akka。所以我们重点讲述 Akka。

Akka 是构建高并发、分布式、弹性消息驱动的 Java 和 Scala 应用的工具集合。官网地址是https://akka.io/ 。

## Akka Hello World

示例代码我已经放到 github 上了，地址为 https://github.com/spetacular/scala-simple-lesson。

下载代码后，用命令行 `cd akkademo` 进入示例代码目录。

依次运行 `stb` 、`compile`、`run`，可以看到类似于如下输出：

```shell
$ sbt
[info] Loading project definition from /Users/didi/Documents/GitHub/scala-simple-lesson/akkademo/project
[info] Loading settings for project akkademo from build.sbt ...
[info] Set current project to akka-demo (in build file:/Users/didi/Documents/GitHub/scala-simple-lesson/akkademo/)
[info] sbt server started at local:///Users/didi/.sbt/1.0/server/bc7cb24c19aa2ba1ff53/sock
sbt:akka-demo> compile
[info] Updating ...
[info] Done updating.
[info] Compiling 1 Scala source to /Users/didi/Documents/GitHub/scala-simple-lesson/akkademo/target/scala-2.12/classes ...
[info] Done compiling.
[success] Total time: 6 s, completed 2019-6-25 17:57:00
sbt:akka-demo> run
[info] Packaging /Users/didi/Documents/GitHub/scala-simple-lesson/akkademo/target/scala-2.12/akka-demo_2.12-1.0.jar ...
[info] Done packaging.
[info] Running com.example.AkkaDemo 
fine thank you
huh?
```

我们看下代码：

```scala
1  package com.example;
2  import akka.actor.{Actor,ActorSystem,Props}
3  
4  class HelloActor extends Actor {
5    def receive = {
6      case "how are you" => println("fine thank you")
7      case _       => println("huh?")
8    }
9  }
10  
11  object AkkaDemo extends App {
12    val system = ActorSystem("HelloSystem")
13    // default Actor constructor
14    val helloActor = system.actorOf(Props[HelloActor], name = "helloactor")
15    helloActor ! "how are you"
16    helloActor ! "Bonjour"
17  }
```

我们先看下调用示例图：

![akka demo调用示例](/images/2019/akka_demo_1.png)

第 4 至 9 行，定义了 `HelloActor` 的类。`HelloActor` 继承在 `Actor` 类，实现了 `receive` 方法。该方法根据传递过来的字符串，做出不同的动作。

第 11 行，`AkkaDemo` 继承了 `App` 类，作为`main class`，是程序执行的入口。

第 12 行，定义了一个 `ActorSystem`，这是所有 `Actor` 的容器。

第 14 行，定义了一个 `Actor`。`Actor` 是一种类似于线程、goroutine的一个事物，是执行调度的一个单位。在创建  `helloActor` 的时候，并没有用 `new` 关键字来实例化，而是用 `actorOf` 来创建一个 `Actor` 的引用。`actorOf` 接收两个参数：配置类 `Props` 和名称。这种方式的优点是在分布式系统上，调用方不用关心实质的 `Actor` 在哪台机器上。

第 15、16行，将`"how are you"`和`"Bonjour"`的消息传递给 `HelloActor`。

这个例子演示了 akka 的 AkkaSystem、Actor、消息传递是如何交互的。但这个例子，仍然是串行处理的。我们将在下面的例子中，演示并行编程。

## Akka Quickstart with Scala

这个例子来自 Akka 官方示例，网址为 [Akka Quickstart with Scala](https://developer.lightbend.com/guides/akka-quickstart-scala/index.html) 。

### 下载

下载方法有两种：

1. 在官网页面  [Lightbend Tech Hub](https://developer.lightbend.com/start/?group=akka&project=akka-quickstart-scala) 点击 `CREATE A PROJECT FOR ME` 
2. 嫌速度慢可以下载 [Scala Simple Lesson]( https://github.com/spetacular/scala-simple-lesson) ，解压后 **akka-quickstart-scala** 目录即是示例代码。

## 配置及运行

解压后的代码，构建脚本可能没有运行权限，需要用 `chmod` 命令添加可运行权限。

```shell
$ cd akka-quickstart-scala
$ chmod u+x ./sbt
$ chmod u+x ./sbt-dist/bin/sbt
```

然后输入 `./sbt` 。这时会下载依赖的包，可能会持续一段时间。

然后在 stb 输入提示符下，输入 `reStart` 来启动示例。类似的输出如下：

```shell
sbt:akka-quickstart-scala> reStart
[info] Updating ...
[info] Done updating.
[info] Compiling 1 Scala source to /Users/didi/Documents/GitHub/scala-simple-lesson/akka-quickstart-scala/target/scala-2.12/classes ...
[info] Done compiling.
[info] Application akka-quickstart-scala not yet started
[info] Starting application akka-quickstart-scala in the background ...
akka-quickstart-scala Starting com.example.AkkaQuickstart.main()
[success] Total time: 5 s, completed 2019-6-25 20:16:03
sbt:akka-quickstart-scala> akka-quickstart-scala [INFO] [06/25/2019 20:16:04.502] [helloAkka-akka.actor.default-dispatcher-5] [akka://helloAkka/user/printerActor] Greeting received (from Actor[akka://helloAkka/user/helloGreeter#266661191]): Hello, Scala
akka-quickstart-scala [INFO] [06/25/2019 20:16:04.502] [helloAkka-akka.actor.default-dispatcher-5] [akka://helloAkka/user/printerActor] Greeting received (from Actor[akka://helloAkka/user/howdyGreeter#1265860474]): Howdy, Akka
akka-quickstart-scala [INFO] [06/25/2019 20:16:04.502] [helloAkka-akka.actor.default-dispatcher-5] [akka://helloAkka/user/printerActor] Greeting received (from Actor[akka://helloAkka/user/howdyGreeter#1265860474]): Howdy, Lightbend
akka-quickstart-scala [INFO] [06/25/2019 20:16:04.502] [helloAkka-akka.actor.default-dispatcher-5] [akka://helloAkka/user/printerActor] Greeting received (from Actor[akka://helloAkka/user/goodDayGreeter#1928998630]): Good day, Play
```

### 代码解读

完整代码如下：

```scala
1  //#full-example
2  package com.example
3  
4  import akka.actor.{ Actor, ActorLogging, ActorRef, ActorSystem, Props }
5  
6  //#greeter-companion
7  //#greeter-messages
8  object Greeter {
9    //#greeter-messages
10    def props(message: String, printerActor: ActorRef): Props = Props(new Greeter(message, printerActor))
11    //#greeter-messages
12    final case class WhoToGreet(who: String)
13    case object Greet
14  }
15  //#greeter-messages
16  //#greeter-companion
17  
18  //#greeter-actor
19  class Greeter(message: String, printerActor: ActorRef) extends Actor {
20    import Greeter._
21    import Printer._
22  
23    var greeting = ""
24  
25    def receive = {
26      case WhoToGreet(who) =>
27        greeting = message + ", " + who
28      case Greet           =>
29        //#greeter-send-message
30        printerActor ! Greeting(greeting)
31        //#greeter-send-message
32    }
33  }
34  //#greeter-actor
35  
36  //#printer-companion
37  //#printer-messages
38  object Printer {
39    //#printer-messages
40    def props: Props = Props[Printer]
41    //#printer-messages
42    final case class Greeting(greeting: String)
43  }
44  //#printer-messages
45  //#printer-companion
46  
47  //#printer-actor
48  class Printer extends Actor with ActorLogging {
49    import Printer._
50  
51    def receive = {
52      case Greeting(greeting) =>
53        log.info("Greeting received (from " + sender() + "): " + greeting)
54    }
55  }
56  //#printer-actor
57  
58  //#main-class
59  object AkkaQuickstart extends App {
60    import Greeter._
61  
62    // Create the 'helloAkka' actor system
63    val system: ActorSystem = ActorSystem("helloAkka")
64  
65    //#create-actors
66    // Create the printer actor
67    val printer: ActorRef = system.actorOf(Printer.props, "printerActor")
68  
69    // Create the 'greeter' actors
70    val howdyGreeter: ActorRef =
71      system.actorOf(Greeter.props("Howdy", printer), "howdyGreeter")
72    val helloGreeter: ActorRef =
73      system.actorOf(Greeter.props("Hello", printer), "helloGreeter")
74    val goodDayGreeter: ActorRef =
75      system.actorOf(Greeter.props("Good day", printer), "goodDayGreeter")
76    //#create-actors
77  
78    //#main-send-messages
79    howdyGreeter ! WhoToGreet("Akka")
80    howdyGreeter ! Greet
81    
82    howdyGreeter ! WhoToGreet("Lightbend")
83    howdyGreeter ! Greet
84    
85    helloGreeter ! WhoToGreet("Scala")
86    helloGreeter ! Greet
87  
88    goodDayGreeter ! WhoToGreet("Play")
89    goodDayGreeter ! Greet
90    
91    //#main-send-messages
92  }
93  //#main-class
94  //#full-example
```

我们先直观地看下这个文件的大体框架，如下图所示。

![akka demo 架构图](/images/2019/hello-akka-architecture.png)

首先 main 类创建了ActorSystem 容器，然后创建了三个 Greeter Actor 实例和一个 Printer Actor 实例。

![akka消息传递](/images/2019/hello-akka-messages.png)

main 类首先将消息传递给三个 Greeter Actor，然后三个 Greeter Actor 分别再将消息传递给 Printer Actor，并由 Printer Actor 将消息打印出来。

下面是详细解读。

第 6 至 34 行，定义了 `Greeter` 的伴友类和伴友对象。注意 `Greeter` 是一个 `Actor`，其 `receive` 方法定义在第 25 至 32 行。

第 12 行定义了 `WhoToGreet` 的样例类：`final case class WhoToGreet(who: String)`  。

第 13 行定义了 `Greet` 的样例类：`case object Greet` 。

`Greeter`、`WhoToGreet`、`Greet` 的关系是怎样的呢？

`Greeter` 意思为礼仪人员。它接收的 `case class` 是 `WhoToGreet` 和 `Greet`。就是说礼仪人员有两个用途：一是组织根据人员组装合适的礼貌用语(WhoToGreet），另一个是将拼好的礼貌用语用嘴巴(Printer)说出去(Greet)。

第 36 至 56 行定义了 `Printer` 的伴友类和伴友对象。

第 41 行定义了 `Greeting` 样例类：`case class Greeting(greeting: String)` 。

第 51 至 54 行定义了 `Printer` 的 `receive` 方法。它接收 `Greeting`，并将 `greeting` 内容打印出来。

第 58 至 94 行为入口方法。

第 63 行定义了名为 `helloAkka` 的 Actor System。

第 69 至 76 行创建了三个 Greeter 的实例：`howdyGreeter`、`helloGreeter`、`goodDayGreeter`。他们会说不同的礼貌用语。

第 78 至 91 行调用 Actor 进行并发处理。我们以第一个调用为例，看一下处理流程。

```scala
howdyGreeter ! WhoToGreet("Akka")
howdyGreeter ! Greet
```

首先将 `WhoToGreet("Akka")` 的消息传递给 `howdyGreeter`，这时会激活如下语句：

```scala
26      case WhoToGreet(who) =>
27        greeting = message + ", " + who
```

此时

```scala
message = "Howdy"
who = "Akka"
greeting = message + ", " + who = "Howdy, Akka"
```

然后将 Greet 的消息传递给 `howdyGreeter`，这时会激活如下语句：

```scala
28      case Greet           =>
29        //#greeter-send-message
30        printerActor ! Greeting(greeting)
31        //#greeter-send-message
```

此时将 `Greet` 的消息通过 `Greeting` 传递给 `PrinterActor`。

最后 PrintActor 收到消息后，将消息打印在日志上。

```scala
51    def receive = {
52      case Greeting(greeting) =>
53        log.info("Greeting received (from " + sender() + "): " + greeting)
54    }
```

### 思考

如果我们多次输入 `reStrart` 来运行该例子，会发现输出的礼貌用语的次序各不相同。这就是说程序是并发执行的。同时需要注意，无论怎么执行，`howdyGreeter` 的两次结果，`Akka` 始终在 `Lightbend` 之前。我们可以得出结论：

* 不同的 `Actor` 直接是并发执行的。
* 同一个 `Actor` 接收到的多个消息，是串行执行的。



# 参考文献

[https://www.geeksforgeeks.org/scala-singleton-and-companion-objects/](https://www.geeksforgeeks.org/scala-singleton-and-companion-objects/)

https://www.tutorialspoint.com/scala/index.htm

https://www.runoob.com/scala/scala-tutorial.html

https://blog.csdn.net/shenlei19911210/article/details/78538255

https://docs.scala-lang.org/zh-cn/tour/pattern-matching.html

https://developer.lightbend.com/guides/akka-quickstart-scala/