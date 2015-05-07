---
layout: post
title: String中的无意识递归
date: 2015-05-07 11:06:56
category: "java"
---

我们知道在Java在所有类的父类是Object,容器自然也不例外。因此容器都有toString()方法，并且覆写了该方法，使容器生成的String结果能够表达容器自身，以及容器所有包含的对象。例如ArrayList的toString()方法，它会遍历ArrayList中包含的所有对象，调用每个对象的toString()方法。

如果你希望toString()方法打印出对象的内存地址，也许会考虑使用this关键字。
来看下面代码:

{% highlight java %}
class User {

    @Override
    public String toString() {
        return "User{}" + this;
    }
}
public class TestMain {
    
    public static void main(String[] args) {

        List<User> users = new ArrayList<User>();

        for (int i = 0; i < 10; i++)
            users.add(new User());
        System.out.println(users);

    }
}
{% endhighlight %}
如果你只是简单的创建User对象，并打印出来，会得到非常长的异常，在上面代码中，把User对象加入到容器中，然后打印改ArrayList容器，会得到同样的异常。

{% highlight java %}
Exception in thread "main" java.lang.StackOverflowError
	at java.lang.AbstractStringBuilder.<init>(AbstractStringBuilder.java:63)
	at java.lang.StringBuilder.<init>(StringBuilder.java:85)
	at com.zjiajun.java.User.toString(TestMain.java:15)
	at java.lang.String.valueOf(String.java:2847)
	at java.lang.StringBuilder.append(StringBuilder.java:128)
	at com.zjiajun.java.User.toString(TestMain.java:15)
	at java.lang.String.valueOf(String.java:2847)
	at java.lang.StringBuilder.append(StringBuilder.java:128)
	....
	....
	....
{% endhighlight %}

关键就在于**"User{}" + this**,这里发生了自动类型转换，由User类型转换成String类型。编译器看到一个String对象后面跟着一个“＋”(重载操作符，一个操作符在应用于特定的类时，被赋予了特殊的意义。在java中，用于String的“＋”和“＋＝”是java中仅有的两个重载操作符)，而再后面不是String，于是编译器试着将this转换成一个String。它怎么转换呢？正是通过调用this上的toString()方法，于是就发生了递归调用。
**如果真的想打印出对象的内存地址，应该调用Object.toString()方法。所以不应该使用this,而是应该调用super.toString()方法。**

{% highlight java %}
class User {

    @Override
    public String toString() {
        return "User{}" + super.toString();
    }
}
{% endhighlight %}

{% highlight java %}
[User{}com.zjiajun.java.User@5c1d29c1, User{}com.zjiajun.java.User@7ea06d25,......]
{% endhighlight %}

其实不用覆写User的toString()方法，也会打印出内存地址，默认使用的Object的toString()方法。

原创文章转载请注明出处: [String中的无意识递归](http://9leg.com/java/2015/05/07/string-recursive.html)
