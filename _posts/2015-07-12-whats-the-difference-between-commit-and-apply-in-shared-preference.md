---
layout: post
title: SharedPreference.Editor中commit和apply区别
date: 2015-07-12 18:53:30
category: "android"
---

学Android也有段时间了,至今还没写过文章,可能是我懒吧(其实就是懒- -),今天在练习项目的时候，被IDEA一个warning弄懵了,看下图:

![commit-warning](/images/posts/SharedPreference-Editor-commit-warning.jpg)

首先*commit()*方法是为SharedPreference保存数据用的,IDEA警告意思是说,建议用*apply()*方法代替。*commit()*方法保存数据是马上执行的,而*apply()*方法会在后台执行。

这是毛意思呢? 反正我先注释了*commit()*方法,添加了*apply()*方法,警告信息就没有了,why? why? why?，这是为什么?

然后google大法搜索了下,这两个方法区别是这样的:

* commit方法有boolean返回值,表示保存是否成功的.
* apply方法是void的.
* commit方法是同步执行保存.
* apply方法是异步执行保存(这就是IDEA的警告意思吧).

**应该到底用哪个方法呢? 我的结论是视实际情况而定,如不关心是否保存成功,就可以用异步的apply方法,相反,在乎保存返回值的,则用commit方法.如果出现并发情况,那么肯定是用异步的apply方法,这是如果用了commit方法的话，就有可能会导致阻塞.
apply方法是现将数据立马存到内存中,然后会异步的去保存到目录文件去.**

抄一段API文档中的说明:

Unlike commit(), which writes its preferences out to persistent storage synchronously, apply() commits its changes to the in-memory SharedPreferences immediately but starts an asynchronous commit to disk and you won't be notified of any failures. If another editor on this SharedPreferences does a regular commit() while a apply() is still outstanding, the commit() will block until all async commits are completed as well as the commit itself.

As SharedPreferences instances are singletons within a process, it's safe to replace any instance of commit() with apply() if you were already ignoring the return value.

[官方API-apply()方法解释(需翻墙)](http://developer.android.com/intl/zh-cn/reference/android/content/SharedPreferences.Editor.html#apply()){:target="_blank"}

在获取SharedPreferences对象时,大家会用那种方式呢?

* Activity.getPreferences(int mode)
* PreferenceManager.getDefaultSharedPreferences(Context context);
* ContextWrapper.getSharedPreferences(String name, int mode)

然并卵,1和2其实还是调用的第三个方法,如下:

{% highlight java %}

public SharedPreferences getPreferences(int mode) {
       return getSharedPreferences(getLocalClassName(), mode);
}
    
{% endhighlight %}

Activity的方法,调用的还是父类的*getSharedPreferences*方法,就是第三个方法，只不过这里保存文件的文件名称,使用默认的类名罢了.

{% highlight java %}

public static SharedPreferences getDefaultSharedPreferences(Context context) {
        return context.getSharedPreferences(getDefaultSharedPreferencesName(context),
                getDefaultSharedPreferencesMode());
}
   
private static String getDefaultSharedPreferencesName(Context context) {
        return context.getPackageName() + "_preferences";
}

private static int getDefaultSharedPreferencesMode() {
        return Context.MODE_PRIVATE;
}
{% endhighlight %}

第二种,在明显不过了,tmd还是调用第三个啊,文件名称和操作模式mode都有默认值。


{% highlight java %}
public SharedPreferences getSharedPreferences(String name, int mode) {
        return mBase.getSharedPreferences(name, mode);
}
{% endhighlight %}

这是祖宗,自定义文件名称,操作模式可选,完美.


原创文章转载请注明出处：[SharedPreference.Editor中commit和apply区别](http://9leg.com/android/2015/07/12/whats-the-difference-between-commit-and-apply-in-shared-preference.html)
