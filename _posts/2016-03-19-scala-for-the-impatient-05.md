---
layout: post
title: 快学scala笔记——映射和元组
date: 2016-03-19 15:30:22
category: "scala"
---

- 映射是键/值对偶的集合。

- 元组是N个对象的聚集，并不一定要相同的类型。

- 构建一个不可变的映射。

```scala

val scores = Map("scala"->10,"java"->20) //不可变map,值不能改变

```

- 构建一个可变的映射。

```scala

val imScores = collection.mutable.Map("python"->10,"php"->20) //可变map

```

- 如果想构建一个空的映射，需要选定一个映射的实现并给出类型参数。

```scala

val emptyMap = new collection.mutable.HashMap[String,Int] //空map

```

- 使用 -> 操作符创建对偶。

- 也可以使用如下的方式来定义。

```scala

val s = Map(("scala",10),("java",20))

```

- 显然 -> 操作符比圆括号更易读。

- 获取映射中的值。

```scala

val scala = scores("scala")

//如果映射不包含请求中使用的键，则会抛出异常

```

- 检查映射中是否有某个指定的键。

```scala

val scala = if (scores.contains("scala") scores("scala") else 0

```

- 上面的组合十分普遍，所以有个快捷方法。

```scala

val scala = scores.getOrElse("scala",0)

```

- 映射.get(键)调用返回的是Option对象，要么是Some，要么是None。

- 更新映射中的值。

在可变映射中，更新某个映射的值或者添加一个新的映射关系，做法是在＝号左侧使用()。

```scala

imScores("scala") = 100  //更新scala键对应的值

imScores("php") = 50     //增加新的映射关系

imScores += ("python" -> 200,"swift" -> 300) //使用 += 操作来添加多个关系

imScores -= "python" //使用 -= 移除某个键对应的值

```

不能更新一个不可变的映射，但可以做一些同样的操作。

```scala

val scores2 = scores + ("scala"->100,"php"->300)

//原不可变映射scores做的scala被更新，php被添加进来

var scores =  Map("scala"->10,"java"->20)

scores = scores + ("scala" -> 100,"php" -> 300)

//除了把结果作为新值保存外，也可以使用var变量

scores = scores - "php"

//使用 - 操作符来获取一个新的去掉改键的映射

```

- 迭代映射。

```scala

for ((k,v) <- 映射) //处理k和v

```

出于某种原因，只需要访问键和值，可以像java一样，使用keySet和values方法

- 反转一个映射，交换键和值的位置。

```scala

for ((k,v) <- 映射) yield (v,k)

```

- 已排序映射

```scala

val scores = collection.immutable.SortedMap("scala"->10,"java"->20)

```

- 按插入顺序访问所有的键，使用LinkedHashMap。

```scala

val months = collection.immutable.LinkedHashMap("1"->1,"2"->2,"3"->3)

```

- 与java的互相操作

```scala

import scala.collection.JavaConversions.mapAsScalaMap

//通过java方法调用得到一个java的映射，转换成scala的映射，以便使用更方便的映射api

val scores: mutable.Map[String,Int] = new java.util.TreeMap[String,Int]



//得到从java.util.Properties到Map[String,String]的转换

import scala.collection.JavaConversion.propertiesAsScalaMap

val props: scala.collection.Map[String,String] = System.getProperties()



//scala映射转换为java映射

import scala.collection.JavaConversions.mapAsJavaMap

import java.awt.font.TextAttribute._

val attrs =  Map(FAMILY->"Serif",SIZE->12) //scala映射

val font = new java.awt.Font(attrs) //Font构造器预期接受一个java映射

```

- 元组

```scala

val t = (1,3.14,"scala")

//该元组的类型为Tuple3[Int,Double,java.lang.String]

```

- 访问元组

```scala

val s = t._1

//通过_1, _2, _3访问元组，从1开始，并不是0

```

- 元组用于函数需要返回不止一个值的情况。

- 拉链操作

```scala

val symbols = Array("<","-",">")

val counts = Array(2,10,2)

val paris = symbols.zip(counts)

//输出对偶的数组 Array(("<",2),("-",10),(">",2))

for ((s,n) <- paris) print(s*n)

//对偶就可以一起被处理，会打印<<---------->>



可以用toMap方法将对偶的集合转换成映射

symbols.zip(counts).toMap

//scala.collection.immutable.Map[String,Int] = Map(< -> 2, - -> 10, > -> 2)

```

原创文章转载请注明出处：[快学scala笔记——映射和元组](http://9leg.com/scala/2016/03/19/scala-for-the-impatient-05.html)