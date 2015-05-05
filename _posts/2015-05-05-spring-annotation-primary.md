---
layout: post
title: Spring注解@Primary
date: 2015-05-05 16:05:39
category: "spring"
---

假定，有如下两个类，OperaSinger and MetalSinger。

### MetalSinger类

{% highlight java %}
@Component
public class MetalSinger implements Singer {

    @Override
    public String sing(String lyrics) {
        return "I am singing with DIO voice: "+lyrics;
    }
}
{% endhighlight %}

### OperaSinger类

{% highlight java %}
public class OperaSinger implements Singer {
    @Override
    public String sing(String lyrics) {
        return "I am singing in Bocelli voice: "+lyrics;
    }
}
{% endhighlight %}

### Singer接口

{% highlight java %}
public interface Singer {
    String sing(String lyrics);
}
{% endhighlight %}

现在定义SingerService并且注入Singer

### SingerService类

{% highlight java %}
@Component
public class SingerService {
    private static final Logger logger = LoggerFactory.getLogger(SingerService.class);

    @Autowired
    private Singer singer;

    public String sing(){
        return singer.sing("song lyrics");
    }
}
{% endhighlight %}

你觉得哪个Singer实现类会被注入？结果如下：

{% highlight java %}
I am singing with DIO voice: song lyrics
{% endhighlight %}

这是因为OperaSinger类未定义@Component或者@Service注解，所以Spring对于OperaSinger类完全没想法，现在加上@Component注解：

{% highlight java %}
@Component
public class OperaSinger implements Singer {
    @Override
    public String sing(String lyrics) {
        return "I am singing in Bocelli voice: "+lyrics;
    }
}
{% endhighlight %}

然后，会得到异常信息:

{% highlight java %}
org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type [main.service.Singer] is 
defined: expected single matching bean but found 2: metalSinger,operaSinger
{% endhighlight %}

原因显而易见，如果有不止一个bean的相同类型，并且使用了@Autowired注解根据类型绑定bean，就会得到这个异常信息，Spring不知道如何去选择。
现在给OperaSinger类加上@Primary注解。

{% highlight java %}
@Primary
@Component
public class OperaSinger implements Singer {

    @Override
    public String sing(String lyrics) {
        return "I am singing in Bocelli voice: "+lyrics;
    }
}
{% endhighlight %}

结果如下：

{% highlight java %}
I am singing in Bocelli voice: song lyrics
{% endhighlight %}

这是因为OperaSinger类加上了@Primary注解，Primary主要的，从单词意思就可以理解。
另外解决方法是，使用@Qualifier注解使用value属性指定bean的名称。

{% highlight java %}
@Component
public class SingerService {
    private static final Logger logger = LoggerFactory.getLogger(SingerService.class);

    @Autowired
    @Qualifier(value = "operaSinger")
    private Singer singer;

    public String sing(){
        return singer.sing("song lyrics");
    }
}
{% endhighlight %}

原创文章转载请注明出处: [Spring注解@Primary](http://9leg.com/spring/2015/05/05/spring-annotation-primary.html)

[英文原文链接](http://www.javacodegeeks.com/2015/04/spring-annotations-i-never-had-the-chance-to-use-part-1-primary.html){:target="_blank"}
