---
layout: post
title: this的由来和正确的使用方式
date: 2015-01-01 22:27:30
category: "java"
---

this关键字在java中，通常都是指“这个对象”或者“当前对象”的含义，它本身表示对当前对象的引用。

### this的由来

那为什么会有这个关键字呢？

来看下Think in Java中对this的描述，如果有同一类型的两个对象，分别是a和b。你可能想知道，如何才能让过这两个对象都能调用peel()方法呢：

{% highlight java %}
class Foo {
	void peel(int i) { }
}
public class FooPeel {
	public static void main(String [] args) {
		Foo a = new Foo(),
			b = new Foo();
		f1.peel(1);
		f2.peel(2);
	}
}
{% endhighlight %}
如果只有一个peel方法，它如何知道是被a还是b所调用的呢？
为了能用简便、面向对象的语法来编写代码——既“发送消息给对象”，编译器做了一些幕后工作。它暗自把“所操纵对象的引用”作为第一个参数传递给peel()。所以上述两个方法的调用就变成了这样：

{% highlight java %}
Foo.peel(a,1);
Foo.peel(b,2);
{% endhighlight %}

这是内部的表示形式。当然我们肯定不能这样写代码，也不会编译通过，这样写就是帮助大家了解实际所发生的事情。
那么问题来了，如果你希望在方法的内部获得对当前对象的引用，该如何获得这个引用呢？由于这个引用是由编译器“偷偷”传入的，是没有标识符可用。所以，为此有个专门的关键字，是的，它就是this。this关键字只能在方法内部使用，表示对“调用方法的那个对象”的引用。

### this的正确使用方式
首先，我们来看下面两段代码：

{% highlight java %}
class Apple {
    void getApple(){
    }
    void getBigApple() {
        this.getApple();
    }
}
{% endhighlight %}

{% highlight java %}
class Apple {
    void getApple(){
    }
    void getBigApple() {
        getApple();
    }
}
{% endhighlight %}

上面两段代码都演示了在方法内部调用同一个类的另一个方法，这两者写法没有对错。但是**没有加this调用才是最优的**。有些人喜欢在每一个方法调用和字段引用前，认为这样“更清楚更明确”。其实不然，使用高级语言的原因之一就是它们能帮我们做一些事情，在这里编译器能帮我们自动添加。
只有当需要明确指出对当前对象的引用时，才需要使用this关键字。例如，返回对当前对象的引用时，就常常在return语句里这样写：

{% highlight java %}
public class Apple {
	int i = 0;
	Apple incr() {
		i++;
		return this;
	}
}
{% endhighlight %}
要是你把this放在一些没必要的地方，会使读你代码的人不知所错，因为别人写的代码不会到处使用this，只在必要处使用this。

可能为一个类写了多个构造器，有时可能想在一个构造器中调用另一个构造器，以避免重复代码。可用this做到这一点。
在构造器中，如果为this添加了参数列表，那么就有了不同的含义。这将产生对符合此参数列表的某个构造器的明确调用。

{% highlight java %}
public class Apple {
	int count = 0;
	String s = "initial value";
	Apple(int value) {
		count = value;
	}
	Apple(String ss) {
		s = ss;
	}
	Apple(String s,int value) {
		this(value);
		//this(s); can't call two
		this.s = s; 
	}
	Apple () {
		this("9leg",88);
	}
	public static void main(String [] args) {
		Apple apple = new Apple();
	}
}
{% endhighlight %}
上面的代码很简单，构造器*Apple(String s,int value)*表明，尽管可以用this调用一个构造器，但却不能调用两个。此外，必须将构造器调用置于最起始处，否则编译器会报错。
这里还展示了this的另一种用法。由于参数s的名称和数据成员s的名字相同，会产生歧义，使用*this.s*来代表数据成员就能解决。

原创文章转载请注明出处：[this的由来和正确的使用方式](http://www.9leg.com/java/2015/01/01/how-to-properly-use-this.html)