---
layout: post
title: 快学scala笔记——控制结构和函数上篇
date: 2016-02-21 18:52:35
category: "scala"
---

- 在scala中，几乎所有构造出来的语法结构都有值。

- scala中if/else表达式有值，这个值就是跟在if或else之后的表达式的值。

```scala

if (x > 0) 1 else -1

```

上述表达式的值是1或-1，具体是哪个取决于x的值。

- 可以将if/else表达式赋值给变量。

```scala

val x = if (x > 0) 1 else -1

if (x > 0) s = 1 else s = -1

```

上述的表达式第一种更好，因为可以用来初始化一个val，而第二种写法当中，s必须是var的。

- scala没有java的三目运算符(?:)，都使用if/else表达式。

- 在scala中，每个表达式都有类型。

- if (x > 0) 1 else -1 的类型是Int，因为两个分支的类型都是Int。

- if ( x > 0) "scala" else -1 混合类型表达式。表达式的类型是两个分支类型的公共超类型，一个分支是java.lang.String，另一个分支是Int，公共超类型是Any。

- if ( x > 0) 1没有else部分，有可能if语句没有输出值。但在scala中，每个表达式都应该有某种值，

解决方案是引入Unit类型，写做()。等价于 if ( x > 0 ) 1 else ()。

- ()表示无有用的值，可以当做java中的void，但从技术上讲，Unit有一个表示"无值"的值。

- 在REPL中粘贴成块的代码，可以键入:paste，把代码粘贴进去，按下ctrl+d，代码当作一个整体来分析，不会担心REPL的"近视"问题，p15。

- 在scala中，行尾的位置不需要分号，但也可以加上，个人习惯问题。

- 但在单行写下多语句时需要用分号。

- {}块包含一系列表达式，结果也是一个表达式，块中最后一个表达式的值就是块的值。

- 在scala中，赋值动作本身是没有值的，严格的说，值是Unit类型，写做()。

```scala

scala> val s = {var a = 10;a=100}

s: Unit = ()

```

a=100的值()，a的值100。由于赋值语句的值是Unit类型，所以别把他们串接在一起，x＝y＝1，这是错误的，y＝1的值是()，除非你想把Unit类型的值赋给x。

- readLine函数从控制台读取一行输入，带一个参数为提示字符串。

- 如果要读取数组，Boolean或字符，可以用readInt，readDouble，readByte，readShort，readLong，readFloat，readBoolean或者readChar。



原创文章转载请注明出处：[快学scala笔记——控制结构和函数上篇](http://9leg.com/scala/2016/02/19/scala-for-the-impatient-02.html)