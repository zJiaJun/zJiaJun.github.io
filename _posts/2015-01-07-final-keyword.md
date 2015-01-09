---
layout: post
title: Java的final关键字
date: 2015-01-07 20:57
category: "java"
---

编程语言都有某种方法，来向编译器告知一块数据是恒定不变的。有时数据的恒定不变是很有用的，比如：

1. 一个永不改变的编译时常量。
2. 一个在运行时被初始化的值，而你不希望它被改变。

### final数据
对于基本类型final数值恒定不变的；而用于对象引用，final使引用恒定不变。一旦引用被初始化指向一个对象，就无法再把它改为指向另一个对象。而对象其本身却是可以被修改的。

### final参数
java允许在参数列表中以声明的方式将参数指定为final。这表示你无法在方法中更改参数引用所指向的对象。

{% highlight java %}
class A {
	public void spin()
}
public class B {
	void with(final A a){
		// a = new A(); //wrong -- a is final
	}
	void without(A a){
		a = new A();//ok -- a not final
		a.spin();
	}
	int f(final int i) {
		i++;//can't change
		int ii = i + 1;//ok
		return ii;
	}
}
{% endhighlight %}

你可以读参数，但却无法修改参数。

### final方法
方法声明final时，表示把方法锁定，以防任何继承类修改它的含义。这是出于设计的考虑：想要确保在继承中使方法行为保持不变，并且不会被覆盖。
**有一点需要注意。类中所有的private方法都隐式地指定为final的。由于无法取用private方法，所以也就无法覆盖它。可以对private方法添加final修饰词，但这是毫无疑义的。**

### final类
当将某个类的整体定义为final时，就表示你不打算继续该类，而且也不允许别人这样做。换句话说，对该类的设计永不需要做任何变动,或者出于安全的考虑，不希望它有子类。
由于final类禁止继承，所以final类中所有的方法都是隐式指定为是final的，因为无法覆盖它们。

参考ThinkInJava

原创文章转载请注明出处：[Java的final关键字](http://www.9leg.com/java/2015/01/07/final-keyword.html)
