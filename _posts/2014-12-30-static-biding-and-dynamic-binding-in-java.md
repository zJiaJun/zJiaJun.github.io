---
layout: post
title:  Java中的静态绑定和动态绑定
date:   2014-12-30 12:55:11
category: "java"
---



一个Java程序的执行要经过编译和执行（解释）这两个步骤，同时Java又是面向对象的编程语言。当子类和父类存在同一个方法，子类重写了父类的方法，程序在运行时调用方法是调用父类的方法还是子类的重写方法呢，这应该是我们在初学Java时遇到的问题。这里首先我们将确定这种调用何种方法实现或者变量的操作叫做绑定。

在Java中存在两种绑定方式，一种为静态绑定，又称作早期绑定。另一种就是动态绑定，亦称为后期绑定。

###区别对比

* 静态绑定发生在编译时期，动态绑定发生在运行时
*  使用private或static或final修饰的变量或者方法，使用静态绑定。而虚方法（可以被子类重写的方法）则会根据运行时的对象进行动态绑定。
* 静态绑定使用类信息来完成，而动态绑定则需要使用对象信息来完成。
* 重载(Overload)的方法使用静态绑定完成，而重写(Override)的方法则使用动态绑定完成。

###重载方法的示例

这里展示一个重载方法的示例。

{% highlight java %} 
public class TestMain {
  public static void main(String[] args) {
      String str = new String();
      Caller caller = new Caller();
      caller.call(str);
  }
  static class Caller {
      public void call(Object obj) {
          System.out.println("an Object instance in Caller");
      } 
      public void call(String str) {
          System.out.println("a String instance in in Caller");
      }
  }
}
{% endhighlight %}

执行的结果为
{% highlight java %}
22:19 $ java TestMain
a String instance in in Caller
{% endhighlight %}
在上面的代码中，call方法存在两个重载的实现，一个是接收Object类型的对象作为参数，另一个则是接收String类型的对象作为参数。str是一个String对象，所有接收String类型参数的call方法会被调用。而这里的绑定就是在编译时期根据参数类型进行的静态绑定。

###验证

光看表象无法证明是进行了静态绑定，使用javap发编译一下即可验证。

{% highlight java %}
22:19 $ javap -c TestMain
Compiled from "TestMain.java"
public class TestMain {
  public TestMain();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/String
       3: dup
       4: invokespecial #3                  // Method java/lang/String."<init>":()V
       7: astore_1
       8: new           #4                  // class TestMain$Caller
      11: dup
      12: invokespecial #5                  // Method TestMain$Caller."<init>":()V
      15: astore_2
      16: aload_2
      17: aload_1
      18: invokevirtual #6                  // Method TestMain$Caller.call:(Ljava/lang/String;)V
      21: return
}
{% endhighlight %}
看到了这一行18: invokevirtual #6 // Method TestMain$Caller.call:(Ljava/lang/String;)V 确实是发生了静态绑定，确定了调用了接收String对象作为参数的caller方法。

重写方法的示例

{% highlight java %}
public class TestMain {
  public static void main(String[] args) {
      String str = new String();
      Caller caller = new SubCaller();
      caller.call(str);
  }
  
  static class Caller {
      public void call(String str) {
          System.out.println("a String instance in Caller");
      }
  }
  
  static class SubCaller extends Caller {
      @Override
      public void call(String str) {
          System.out.println("a String instance in SubCaller");
      }
  }
}
{% endhighlight %}
执行的结果为

{% highlight java %}
22:27 $ java TestMain
a String instance in SubCaller
{% endhighlight %}
上面的代码，Caller中有一个call方法的实现，SubCaller继承Caller，并且重写了call方法的实现。我们声明了一个Caller类型的变量callerSub，但是这个变量指向的时一个SubCaller的对象。根据结果可以看出，其调用了SubCaller的call方法实现，而非Caller的call方法。这一结果的产生的原因是因为在运行时发生了动态绑定，在绑定过程中需要确定调用哪个版本的call方法实现。

###验证

使用javap不能直接验证动态绑定，然后如果证明没有进行静态绑定，那么就说明进行了动态绑定。

{% highlight java %}
22:27 $ javap -c TestMain
Compiled from "TestMain.java"
public class TestMain {
  public TestMain();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/String
       3: dup
       4: invokespecial #3                  // Method java/lang/String."<init>":()V
       7: astore_1
       8: new           #4                  // class TestMain$SubCaller
      11: dup
      12: invokespecial #5                  // Method TestMain$SubCaller."<init>":()V
      15: astore_2
      16: aload_2
      17: aload_1
      18: invokevirtual #6                  // Method TestMain$Caller.call:(Ljava/lang/String;)V
      21: return
}
{% endhighlight %}

正如上面的结果，18: invokevirtual #6 // Method TestMain$Caller.call:(Ljava/lang/String;)V这里是TestMain$Caller.call而非TestMain$SubCaller.call，因为编译期无法确定调用子类还是父类的实现，所以只能丢给运行时的动态绑定来处理。

###当重载遇上重写

下面的例子有点变态哈，Caller类中存在call方法的两种重载，更复杂的是SubCaller集成Caller并且重写了这两个方法。其实这种情况是上面两种情况的复合情况。

下面的代码首先会发生静态绑定，确定调用参数为String对象的call方法，然后在运行时进行动态绑定确定执行子类还是父类的call实现。

{% highlight java %}
public class TestMain {
  public static void main(String[] args) {
      String str = new String();
      Caller callerSub = new SubCaller();
      callerSub.call(str);
  }
  
  static class Caller {
      public void call(Object obj) {
          System.out.println("an Object instance in Caller");
      }
      
      public void call(String str) {
          System.out.println("a String instance in in Caller");
      }
  }
  
  static class SubCaller extends Caller {
      @Override
      public void call(Object obj) {
          System.out.println("an Object instance in SubCaller");
      }
      
      @Override
      public void call(String str) {
          System.out.println("a String instance in in SubCaller");
      }
  }
}
{% endhighlight %}
执行结果为

{% highlight java %}
22:30 $ java TestMain
a String instance in in SubCaller
{% endhighlight %}
###验证

由于上面已经介绍，这里只贴一下反编译结果啦

{% highlight java %}
22:30 $ javap -c TestMain
Compiled from "TestMain.java"
public class TestMain {
  public TestMain();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/String
       3: dup
       4: invokespecial #3                  // Method java/lang/String."<init>":()V
       7: astore_1
       8: new           #4                  // class TestMain$SubCaller
      11: dup
      12: invokespecial #5                  // Method TestMain$SubCaller."<init>":()V
      15: astore_2
      16: aload_2
      17: aload_1
      18: invokevirtual #6                  // Method TestMain$Caller.call:(Ljava/lang/String;)V
      21: return
}
{% endhighlight %}
好奇问题

非动态绑定不可么？

其实理论上，某些方法的绑定也可以由静态绑定实现。比如
{% highlight java %}
public static void main(String[] args) {
      String str = new String();
      final Caller callerSub = new SubCaller();
      callerSub.call(str);
}
{% endhighlight %}
比如这里callerSub持有subCaller的对象并且callerSub变量为final，立即执行了call方法，编译器理论上通过足够的分析代码，是可以知道应该调用SubCaller的call方法。

但是为什么没有进行静态绑定呢？
假设我们的Caller继承自某一个框架的BaseCaller类，其实现了call方法，而BaseCaller继承自SuperCaller。SuperCaller中对call方法也进行了实现。

假设某框架1.0中的BaseCaller和SuperCaller

{% highlight java %}
static class SuperCaller {
  public void call(Object obj) {
      System.out.println("an Object instance in SuperCaller");
  }
}
  
static class BaseCaller extends SuperCaller {
  public void call(Object obj) {
      System.out.println("an Object instance in BaseCaller");
  }
}
{% endhighlight %}
而我们使用框架1.0进行了这样的实现。Caller继承自BaseCaller，并且调用了super.call方法。

{% highlight java %}
public class TestMain {
  public static void main(String[] args) {
      Object obj = new Object();
      SuperCaller callerSub = new SubCaller();
      callerSub.call(obj);
  }
  
  static class Caller extends BaseCaller{
      public void call(Object obj) {
          System.out.println("an Object instance in Caller");
          super.call(obj);
      }
      
      public void call(String str) {
          System.out.println("a String instance in in Caller");
      }
  }
  
  static class SubCaller extends Caller {
      @Override
      public void call(Object obj) {
          System.out.println("an Object instance in SubCaller");
      }
      
      @Override
      public void call(String str) {
          System.out.println("a String instance in in SubCaller");
      }
  }
}
{% endhighlight %}
然后我们基于这个框架的1.0版编译出来了class文件，假设静态绑定可以确定上面Caller的super.call为BaseCaller.call实现。

然后我们再次假设这个框架1.1版本中BaseCaller不重写SuperCaller的call方法，那么上面的假设可以静态绑定的call实现在1.1版本就会出现问题，因为在1.1版本上super.call应该是使用SuperCall的call方法实现，而非假设使用静态绑定确定的BaseCaller的call方法实现。

所以，有些实际可以静态绑定的，考虑到安全和一致性，就索性都进行了动态绑定。

得到的优化启示？

由于动态绑定需要在运行时确定执行哪个版本的方法实现或者变量，比起静态绑定起来要耗时。

**所以在不影响整体设计，我们可以考虑将方法或者变量使用private，static或者final进行修饰。**

[转载](http://droidyue.com/blog/2014/12/28/static-biding-and-dynamic-binding-in-java/){:target="_blank"}