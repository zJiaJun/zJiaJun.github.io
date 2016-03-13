---
layout: post
title: 快学scala笔记——数组相关操作
date: 2016-03-13 20:01:22
category: "scala"
---

- 长度不变的数组

```scala

val nums = new Array[Int](10)

```

- 已提供初始值就不需要new

```scala

﻿val s = Array("java","scala")

```

- 使用()而不是[]访问元素

```scala

s(0) = "github"

```

- 在jvm中，scala的Array以java数组方式实现

- 变长数组，数组缓冲

```scala

val ab = ArrayBuffer[Int]()

ab += 1

ab += (2,3,4,5)

ab ++= Array(6,7)

println(ab)

ab.trimEnd(2) //移除最后2个元素

println(ab)

ab.trimStart(1) //移除头部1个元素

println(ab)

ab.insert(0,1)//在下标0之前插入

println(ab)

ab.insert(ab.length,6,7,8) //在最后插入任意多的元素

println(ab)

ab.remove(ab.length - 1) //移除最后个元素

println(ab)

ab.remove(2,2) //在下标2的位置移除2个元素

println(ab)



val newArray = ab.toArray //ArrayBuffer转Array

newArray.foreach(println)

val newBuffer = newArray.toBuffer //Array转ArrayBuffer

println(newBuffer)

```

- 数组循环

```scala

for (i <- 0 until newArray.length) //可以用newArray.indices

    println(i + ":" + newArray(i))

println("--------")

for (i <- 0 until (newArray.length,2)) //每2个元素一跳

    println(i + ":" + newArray(i))

println("--------")

for (i <- 0 until newArray.length reverse) //从数组的尾端开始遍历

    println(i + ":" + newArray(i))

println("--------")

 for (element <- newArray) //不需要用下标,可以直接访问元素,和java的"增强"for循环相似

    println(element)

```

- 数组转换

```scala

val a = ArrayBuffer(1,2,3,4,5,6)

val result = for (i <- a) yield i * 2 //产生新的ArrayBuffer,不会修改原有ArrayBuffer

println(a)

println(result)



val result2 = for(i <- a if i % 2 == 0) yield i * 2 //守卫,for中的if条件,对偶数翻倍

println(result2)

val result3 = a.filter(_ % 2 == 0).map(_*2) //另一种风格,函数风格

println(result3)

```

- 常用算法

```scala

val aa = Array(3,2,1)

println(aa.sum) //6

println(aa.min) //1

println(aa.max) //3

val sort: Any = scala.util.Sorting.quickSort(aa) //可以直接对数组排序,但不能对数组缓冲排序



val as = Array("array","hello","little")

println(as.max) //little



val saa = aa.sorted //不会修改原始的版本

println(saa.mkString("|")) //1|2|3



val sw = aa.sortWith(_ > _) //提供一个比较函数

println(sw.mkString("<",",",">")) // <3,2,1>

```

- 要使用sum方法，元素类型必须是数值类型，要么是整型，要么是浮点数或者BigInteger/BigDecimal

- min和max输出数组或数组缓冲中最小和最大的元素

- sorted方法将数组或数组缓冲排序并返回经过排序的数组或数组缓冲，这个过程不会修改原始版本

- 还可以提供一个比较函数，不过要使用sortWith方法

- 对于min、max和quickSort方法，元素类型必须支持比较操作，包括了数字，字符串以及其他带有Ordered特质的类型

- 想要显示数组或数组缓冲的内容，可以用mkString方法，允许指定元素之间的分隔符。还有个重载版本可以指定前缀和后缀

- 对数组Array的操作方法都会被转换成ArrayOps对象

- 多维数组使用Array.ofDim()方法

```scala

val matrix = Array.ofDim[Int](2,3) //2行 3列

//Array[Array[Int]] = Array(Array(0, 0, 0), Array(0, 0, 0))

matrix(0)(0) = 1 //第1行 第1列=1 Array(Array(1, 0, 0), Array(0, 0, 0))

matrix(1)(0) = 4 //第2行 第1列=4 Array(Array(1, 0, 0), Array(4, 0, 0))

println(matrix(0).mkString("-")) //1-0-0

println(matrix(1).mkString("-")) //4-0-0

```

- 与java的互相操作

```scala

import scala.collection.JavaConversions.bufferAsJavaList //buffer转javaList

import scala.collection.mutable.ArrayBuffer

val command = ArrayBuffer("ls","-all","/")

/* 构建java原生的List

val list = new java.util.ArrayList[String]()

list.add("jps")

*/

 val pb = new ProcessBuilder(command) //入参是List[String],ArrayBuffer被隐式转换成java的List

val process = pb.start()

val stream = Source.fromInputStream(process.getInputStream,"UTF-8")

println(stream.mkString)



import scala.collection.JavaConversions.asScalaBuffer //返回List,自动转换成一个Buffer,注意不是ArrayBuffer

import scala.collection.mutable.Buffer

val buffer: Buffer[String] = pb.command() //return List[String] => Buffer[String]

println(buffer.mkString)

```

- java的ProcessBuilder类有一个构造器，入参是List<String>。当然在代码中可以直接使用java的ArrayList来实现，就像上述代码传入注释掉的list变量。其实完全可以引入scala的隐式转换方法，scala.collection.JavaConversions.bufferAsJavaList，这样可以在代码中使用scala的数组缓冲，在调用java方法时，自动的包装成java列表

- 反过来是一样的，pb.command()返回List<String>。可以引入JavaConversions.asScalaBuffer，List<String>将自动转换为Buffer[String]，注意并不是ArrayBuffer


原创文章转载请注明出处：[快学scala笔记——数组相关操作](http://9leg.com/scala/2016/03/13/scala-for-the-impatient-04.html)