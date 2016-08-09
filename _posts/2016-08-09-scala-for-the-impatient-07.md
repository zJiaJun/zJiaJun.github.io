---
layout: post
title: 快学scala笔记——对象
date: 2016-08-09 16:45:22
category: "scala"
---

- scala没有静态字段或静态方法,可以用object语法结构达到同样的目的

```scala
object Accounts {
    private var lastNumber = 0
    def newUniqueNumber() = {
        lastNumber += 1;
        lastNumber
    }
}

```

- 对象的构造器在该对象第一次被使用时调用,如Accounts.newUniqueNumber()首次调用时执行构造器,如果一个对象从未被使用,那么构造器也不会执行

- 对象的本质上可以拥有类的所有特性,甚至可以扩展其他类或特质

- 只有一个例外,不能提供构造器参数

- 在java中,类可以有实例方法又可以有静态方法。在scala中,可以通过类和与类同名的伴生对象达到目的


```scala
class Account {
    val id = Account.newUniqueNumber()
    private var balance =  0.0
    def deposit(amount: Double) {
        balance += amount
    }
}

object Account { //伴生对象
    private var lastNumber = 0
    def newUniqueNumber() = {
        lastNumber += 1;
        lastNumber
    }
}

```

- 类和它的伴生对象可以相互访问私有特性,但必须在同一个源文件中

- 类的伴生对象可以被访问,但并不在作用域当中,必须通过Account.newUniqueNumber()而不是直接用newUniqueNumber()

- 对象的apply方法,当遇到Object(参数1,...,参数N),apply方法就会被调用


```scala
class Account private (val id: Int, initialBalance: Double) {
    private var balance = initialBalance
    ...
}

object Account { //伴生对象
    def apply(initialBalance: Double) =
        new Account(newUniqueNumber(),initialBalance)

    ...
}

```

- 这样就可以用val acct = Account(1000.0)构造了

- 应用程序对象

```scala
object Hello {

    def main(args: Array[String]) {
        //doWork
    }
}

```

- 扩展App特质

```scala

object Hello extends App {
    //doWork
}

//命令行参数通过args获取
object Hello extends App {
    if (args.length > 0)
        //doWork
    else
        //doWork
}

```


原创文章转载请注明出处：[快学scala笔记——对象](http://9leg.com/scala/2016/08/09/scala-for-the-impatient-07.html)