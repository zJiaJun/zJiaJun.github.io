---
layout: post
title: 快学scala笔记——基础
date: 2016-02-19 22:20:35
category: "scala"
---

去年,就开始断断续续看scala了,选择了快学scala这本书,当然最全面的肯定是Programming In Scala这本,但觉得不合适入门,可以用于后期提高.

学新东西,一定要去实际使用,然而去年并没有实际scala项目的实战,16年初有幸可以实战了,但发现去年看了后,很多东西都忘记了,当要用了才去翻资料,
上网查询.俗话说的话,好记性不如烂笔头,那就记录并且巩固下吧.


- REPL，就是读取－求值－打印－循环的过程。

- 从技术上讲，scala程序并不是一个解释器。实际发生的是，输入的内容被快速地编译成字节码，这段字节码交由java虚拟机执行。

- val声明的值是常量，无法改变它的内容。

- var声明的值是变量，可以改变他的内容。

- 在scala中，建议多使用val声明。

- 不需要给出值的类型，这个类型可以从用来初始化它的表达式推断出来（在必要的时候，可以指定类型，代码可读性更高）。

- 声明值或变量不做初始化会报错。

- 多个值或变量可以放在一起声明：

```scala
val xmax,ymax = 100
var name,msg = null
```
- scala和java一样，也有7种数值类型：Byte，Short，Int，Long，Float，Double，Char，还有一个Boolean类型。

- 上面类型在scala中都是类，并不刻意区分基本类型和引用类型。

- scala中，不需要包装类型。基本类型和包装类型之间的转换由scala编译器完成。

- scala底层用java.lang.String类表示字符串，它通过StringOps类给字符串追加了很多操作。

- "Hello".intersect("World"),输出lo。这个方法返回2个字符串共通的一组字符。

- 在上面的表达式中，String对象"Hello"被隐式地转换成了StringOps对象。

- scala提供了RichInt——Int，RichDouble——Double，RichChar——Char。

- scala没有java的++和--操作符，需要使用 +=1 和 -=1。

- 没有参数且不改变当前对象的方法不带圆括号()，"Hello".distinct 输出Helo，获取字符串中不重复的字符。

- 伴生对象的apply方法是scala中是构建对象的常用方法。

```scala
"Hello".(4) //简写
"Hello".apply(4)
```

输出都是为o。在StringOps类定义了 def apply(n: Int): Char方法。


原创文章转载请注明出处：[快学scala笔记——基础](http://9leg.com/scala/2016/02/19/scala-for-the-impatient-01.html)