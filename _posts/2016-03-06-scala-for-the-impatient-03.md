---
layout: post
title: 快学scala笔记——控制结构和函数下篇
date: 2016-03-06 13:52:35
category: "scala"
---

-  scala有java相同的while和do循环。

- scala没有for(初始化变量；检查变量是否满足条件；更新变量）循环。

- 一般用for (i <- 表达式) 语法结构

- 变量i遍历<- 右边表达式的所有值。这个遍历具体如何执行，则取决于表达式的类型。

- for循环的变量之前并没有val或var指定，该变量的类型是集合的元素类型，循环变量的作用域一直持续到循环结束。

- until方法返回一个并不包含上限的区间

```scala

val s = "Hello"

var sum = 0

for (i <- 0 until s.length) //i最后的一个取值是s.length - 1

    sum += s(i)

```

上面的例子中，其实并不需要使用下标，可以直接遍历字符序列

```scala

var sum = 0

for (ch <- "Hello") sum += ch

```

- scala的for循环比起java功能要丰富的多

可以以变量 <- 表达式 的形式提供多个生成器，用分号隔开。

```scala

for (i <- 1 to 3; j <- 1 to 3) println((10 * i + j) + " ")

//打印 11 12 13 21 22 23 31 32 33

```

每个生成器都可以带一个守卫，以if开头的boolean表达式。

```scala

for (i <- 1 to 3; j <- 1 to 3 if i != j) println((10 * i + j) + " ")

//打印 12 13 21 23 31 32

```

可以使用任意多的定义，引入可以在循环中使用变量。

```scala

for (i <- 1 to 3; from = 4 - i; j <- from to 3) println((10 * i + j) + " ")

//打印 13 22 23 31 32 33

```

如果for循环的循环体以yield开始，则该循环会构造出一个集合，每次迭代生产集合中的一个值。

```scala

for (i <- 1 to 10) yield i % 3

//生成 Vector(1,2,0,1,2,0,1,2,0,1)

```

这类循环叫做for推导式。

for推导式生成的集合与它第一个生成器是类型兼容的。

```scala

for ( c <- "Hello"; i <- 0 to 1) yield (c + i).toChar

//生成 "HIeflmlmop"

for (i < 0 to 1; c <- "Hello") yield (c + i).toChar

//生成 Vector('H','e','l','l','o','I','f','m','m','p')

```

- scala除了方法外还支持函数。方法对对象进行操作，函数不是。

定义函数

```sclaa

def abs(x: Double) = if (x >= 0) x else -x

```

- 必须给出所有参数的类型。

- 函数只要不是递归的，就不需要指定返回类型，scala编译器可以通过＝符合右侧的表达式类型推断出返回类型。

```scala

//递归必须指定返回类型

def fac(n: Int): Int = if (n <= 0) 1 else n * fac(n - 1)

```

- 如函数体需要多个表达式完成，可以用代码块。块中最后一个表达式的值就是函数的返回值。

- 在调用某些函数时并不显示地给出所有参数值，对于这些函数可以使用默认参数。

```scala

def decorate(str: String,left: String = "[",right: String = "]") = left + str + right

```

这个函数的left和right参数带有默认值 "[" 和 "]"。

- 如果调用decorate("Hello")，会得到"[Hello]"。

- 如果不喜欢默认值，可以给出自己的版本，decorate("Hello","<<<",">>>")。

- 如果相对参数的数量，给出的值不够，默认参数会从后往前逐个应用进来。比如，

decorate("Hello",">>>[")会使用right参数的默认值，得到">>>[Hello]"。

- 可以在提供参数值的时候提供参数名。

```scala

decorate(left = "<<<", str = "Hello", right = ">>>")

// <<<Hello>>>

```

带名参数并不需要跟参数列表的顺序完全一致，带名参数使函数更加可读。

- 可以混用未命名参数和带名参数，只要未命名的参数是排在前面的即可。

```scala

decorate("Hello",right = "]<<<")

//将调用 decorate("Hello","[","]<<<")

```

- 可变长度参数列表的函数,相当于java中String... args，以下是示例语法。

```scala

def sum(args: Int*) = {

    var result = 0

    for (arg <- args) result += arg

    result

}

val s = sum(1,2,3)

```

- 函数得到的是一个类型为Seq的参数。

- val s = sum(1 to 5) //错误的

- val s = sum(1 to 5:_*) //将1 to 5当做参数序列处理

- 调用变长参数且参数类型为Object的java方法，需要手工对基本类型进行转换。

```java

public static String format(String pattern, Object ... arguments)

```

```scala

val str = MessageFormat.format("The answer to {0} is {1}","everything",42.asInstanceOf[AnyRef])

```

- scala对于不返回值的函数有特殊的表示方法。如果函数体包含在花括号当中但没有前面的＝号，那么返回类型就是Unit。这样的函数被称做过程(procedure)。

- 过程不返回值，调用它仅仅是为了它的副作用。

- 代码例子

```scala

def box(s: String) {

    val str = "---" + s + "---"

    println(str)

}

```

- 显示的声明Unit返回类型。

```scala

def box(s: String): Unit = {

    ...

}

```

- 懒值，当val被声明为lazy时，它的初始化将被推迟，直到首次对它取值。

```scala

lazy val words = Source.fromFile("/your/path/words").mkString

```

如果程序不访问words，那么文件也不会被打开。

- 懒值对于开销较大的初始化语句十分有用。

- 可以把懒值当作是介于val和def的中间状态。

```scala

val words = Source.fromFile("/your/path/words").mkString

//在words被定义时即被取值

lazy val words = Source.fromFile("/your/path/words").mkString

//在words首次使用时取值

def words = Source.fromFile("/your/path/words").mkString

//在每一次words被使用是取值

```

- 懒值并不是没有额外的开销。每次访问懒值，都会有一个方法被调用，这个方法将会以线程安全的方法检查该值是否已被初始化。

- scala异常工作机制和java一样。跑出异常

```scala

throw new RuntimeException("Something wrong")

```

- 和java一样，抛出的对象必须是java.lang.Throwable的子类。

- 没有java的受检查异常，就是说不需要声明函数或方法可能会抛出某种异常。

- throw表达式有特殊的类型Nothing。在if/else表达式中很有用。如果一个分支的类型是Nothing，

那么if/else表达式的类型就是另一个分支的类型。

```scala

if (x > 0 ) {

    sqrt(x)

} else throw new RuntimeException("Something wrong")

```

第一个分支类型是Double，第二个分支类型是Nothing，因此if/else表达式类型是Double。

- 捕获异常的语法采用的是模式匹配的语法。

```scala

try {

    println(1/0)

} catch {

    case _: ArithmeticException => println("Something wrong")

    case ex: RuntimeException => ex.printStackTrace()

}

```

和java一样，更通用的异常应该排在更具体的异常之后。

如果不需要使用捕获的异常对象，可以使用_来代替变量名。

- try/finally语句可以释放资源，不论异常有没有发生。



原创文章转载请注明出处：[快学scala笔记——控制结构和函数上篇](http://9leg.com/scala/2016/03/06/scala-for-the-impatient-03.html)