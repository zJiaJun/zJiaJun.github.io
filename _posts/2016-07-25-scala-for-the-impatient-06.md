---
layout: post
title: 快学scala笔记——类
date: 2016-07-25 14:08:22
category: "scala"
---

- 类的方法默认是公有的

- 类并不声明为public,一个scala源文件可以包含多个类,所有这些类都具有公有可见性

- 和java一样,方法可以访问该类的所有对象的私有字段

```scala

class Counter {
    private var value = 0
    def increment() {
        value += 1
    }
    def isLess(other: Counter) = value < other.value
    //可以访问另一个对象的私有字段
}

```
- 因为other同样也是Counter对象,所以访问other.value是合法的

- 可以定义更加严格的访问限制,这样other.value将不被允许

```scala

private[this] var value = 0
//这样,Counter类的方法只能访问到当前对象的value字段,而不能访问同样是Counter类型的其他对象的该字段

```

- 使用@BeanProperty注解自动生成java属性的getXXX/setXXX方法。个人认为在反射的时候有用,约定(javaBean规范)

- 可以有任意多的构造器

- 除了主构造器之外,类可以有任意多的辅助构造器

- 辅助构造器的名称为this,每一个辅助构造器都必须以一个对先前已定义的其他辅助构造器或主构造器的调用开始

```scala

class Person {
    private var name = ""
    private var age = 0

    def this(name: String) { //一个辅助构造器
        this() //调用主构造器
        this.name = name
    }

    def this(name: String,age: Int) { //另一个辅助构造器
        this(name) //调用前一个辅助构造器
        this.age = age
    }
}

```

- 没有显式定义主构造器则自动拥有一个无参的主构造器

- 在scala中,每个类都有主构造器。主构造器并不以this方法定义,而是与类定义交织在一起


```scala

class Person(val name: String, val age: Int) {
    ...
}

```

- 主构造器的参数被编译成字段,值为初始化成构造器时传入的参数

- 主构造器会执行类定义中的所有语句

```scala

class Person(val name: String, val age: Int) {
    println("Just constructed another person")
    def description = name + " is " + age + " years old"
}
```

- 类名之后没有参数,该类具有一个无参主构造器,这样一个构造器仅仅是简单的执行类中的所有语句而已

- 在主构造器中使用默认参数避免过多地使用辅助构造器

```scala

class Person(val name: String = "", val age: Int = 0) {
    ...
}
```

- 私有主构造器

```scala

class Person private(val id : Int) {
    //这样一来就必须通过辅助构造器来构造Person对象
}
```

- 可以在函数中定义函数,类中定义类


原创文章转载请注明出处：[快学scala笔记——类](http://9leg.com/scala/2016/07/25/scala-for-the-impatient-06.html)