---
layout: post
title: Java构造器和枚举的初始化
date: 2015-01-04 21:28:30
category: "java"
---

最近读完了ThinkInJava的第5章初始化与清理，在这里做下简单的总结，以加深影响。

### 构造器初始化
无论创建多少个对象，静态数据都只占用一份存储区域。static关键字不能应用于局部变量，因此它只能作用于域。来看下面的例子：

{% highlight java %}
class Bowl {
  Bowl(int marker) {
    print("Bowl(" + marker + ")");
  }
  void f1(int marker) {
    print("f1(" + marker + ")");
  }
}

class Table {
  static Bowl bowl1 = new Bowl(1);
  Table() {
    print("Table()");
    bowl2.f1(1);
  }
  void f2(int marker) {
    print("f2(" + marker + ")");
  }
  static Bowl bowl2 = new Bowl(2);
}

class Cupboard {
  Bowl bowl3 = new Bowl(3);
  static Bowl bowl4 = new Bowl(4);
  Cupboard() {
    print("Cupboard()");
    bowl4.f1(2);
  }
  void f3(int marker) {
    print("f3(" + marker + ")");
  }
  static Bowl bowl5 = new Bowl(5);
}

public class StaticInitialization {
  public static void main(String[] args) {
    print("Creating new Cupboard() in main");
    new Cupboard();
    print("Creating new Cupboard() in main");
    new Cupboard();
    table.f2(1);
    cupboard.f3(1);
  }
  static Table table = new Table();
  static Cupboard cupboard = new Cupboard();
} 
{% endhighlight %}

Output:
{% highlight java %}
Bowl(1)
Bowl(2)
Table()
f1(1)
Bowl(4)
Bowl(5)
Bowl(3)
Cupboard()
f1(2)
Creating new Cupboard() in main
Bowl(3)
Cupboard()
f1(2)
Creating new Cupboard() in main
Bowl(3)
Cupboard()
f1(2)
f2(1)
f3(1)
{% endhighlight %}

由输出可见，静态初始化只有在必要时刻才会进行。如果不创建Table对象，也不引用Table.b1或Table.b2,那么静态的Bowl b1和b2永远都不会创建。只有在第一个Table对象被创建(或者第一次访问静态数据)的时候，它们才会被初始化。此后，静态对象不会再被初始化。
初始化的顺序是先静态对象，而后是“非静态”对象。从输出结果中可以看到这一点。要执行main()静态方法，必须加载StaticInitialization类，然后其静态域table和cupboard被初始化，这将导致它们对应的类也被加载，并且它们也都包含静态bowl对象，因此bowl随后也被加载。
总结一下对象的创建过程，假设有个名为Dog的类：

1. 即使没有显式地使用static关键字，构造器实际上也是静态方法。因此，当首次创建类型为Dog的对象时，或者Dog类的静态方法或静态域首次被访问时，java解释器必须查找类路径，以定位Dog.class文件。
2. 然后载入Dog.class，有关静态初始化的所有动作读都会执行。因此，静态初始化只在Class对象首次加载的时候进行一次。
3. 当用new Dog()创建对象的时候，首先将在堆上为Dog对象分配足够的存储空间。
4. 这块存储空间会被清零，这就自动地将Dog对象中的所用基本类型数据都设置成了默认值，而引用则被设置成了null。
5. 执行所用出现于字段定义处的初始化动作。
6. 执行构造器。这里面又涉及到很多动作，如继承的时候。

### 枚举初始化
在创建enum时，编译器会自动添加一些有用的特性。例如，它会创建toString()方法，以便可以很方便地显示某个enum实例。编译器还会创建ordinal()方法，用来表示某个特定enum常量的声明顺序，以及static values()方法，用来按照enum常量的声明顺序，产生由这些常量值构成的数组。
enum关键字只是为生成对应的类时，产生了某些编译器行为，因此很大程度上，可以将enum当作其他任何类处理。

原创文章转载请注明出处：[Java构造器和枚举的初始化](http://www.9leg.com/java/2015/01/04/constructor-init.html)