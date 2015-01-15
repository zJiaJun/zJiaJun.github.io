---
layout: post
title: Java字符串转枚举类型
date: 2015-01-09 22:34
category: "java"
---
如下代码有个枚举类，怎样才能将字符串“arin”转成WhoisRIR.ARIN枚举类呢？

### 问题

{% highlight java %}
	WhoisRIR rir = //convert 'arin' to enum WhoisRIR.ARIN?
{% endhighlight %}

{% highlight java %}
	public enum WhoisRIR {
		ARIN("whois.arin.net"),
		RIPE("whois.ripe.net"),
		APNIC("whois.apnic.net");
	}
	private String url;
	
	private WhoisRIR(String url) {
		this.url = url;
	}
	
	public String getUrl() {
		return url;
	}
{% endhighlight %}

###  解决

为了解决这个问题，我们可以使用枚举的*valueof()*方法将字符串转化大写并设置默认的国际化。

{% highlight java %}
import java.util.Locale;

public class EnumTest {

	public static void main(String[] args) {	
	//Solution: Uses valueOf()
	System.out.println(WhoisRIR.valueOf("arin".toUpperCase()));
	//Recommended Solution: add locale	
	WhoisRIR rir =   WhoisRIR.valueOf("ripe".toUpperCase(Locale.ENGLISH));
		System.out.println(rir);
		System.out.println(rir.getUrl());
	}

}
{% endhighlight %}

{% highlight java %}
ARIN
RIPE
whois.ripe.net
{% endhighlight%}

再做下测试，不将字符串转化为大写：
{% highlight java %}
import java.util.Locale;

public class EnumTest {

	public static void main(String[] args) {	
		//error, need convert the String to uppercase
		System.out.println(WhoisRIR.valueOf("arin"));
	}

}
{% endhighlight%}


{% highlight java %}
Exception in thread "main" java.lang.IllegalArgumentException: 
    No enum constant com.mkyong.whois.utils.WhoisRIR.arin
	at java.lang.Enum.valueOf(Unknown Source)
	at com.mkyong.whois.utils.WhoisRIR.valueOf(WhoisRIR.java:1)
	at com.mkyong.whois.utils.TestEnum.main(TestEnum.java:17)
{% endhighlight%}


原创文章转载请注明出处:[Java字符串转枚举类型](http://www.9leg.com/java/2015/01/09/java-convert-string-to-enum-object.html)

[英文原文链接](http://www.mkyong.com/java/java-convert-string-to-enum-object/){:target="_blank"}