---
layout: post
title: 用maven骨架生成项目速度慢的问题
date: 2015-02-01 15:15:40
category: "maven"
---

最近从IntelliJ Idea 14的Community版本切换到Ultimate。

### 问题出现
最近从IntelliJ Idea 14的Community版本切换到Ultimate,key是从网络上下载的。安装之后，在创建maven project时(使用了archetype)，速度慢的令人不敢相信，从Idea的控制台可以看到信息停留在：

{% highlight java %}
[INFO] <<< maven-archetype-plugin:2.2:generate (default-cli) < generate-sources
@ standalone-pom <<<
[INFO]
[INFO] --- maven-archetype-plugin:2.2:generate (default-cli) @ standalone-pom --
-
[INFO] Generating project in Batch mode
{% endhighlight %}
重试了很多次，都在***Generating project in Batch mode***等待，但Idea的状态栏显示还在不停的运行，并没有卡死，大约30分钟之后，才完成项目的创建。

### 问题分析
为什么会等怎么久呢？我先用mvn原生的命令试了一次，

{% highlight java %}
mvn archetype:generate -DgroupId=com.9leg.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
{% endhighlight %}

还是在***Generating project in Batch mode***等了很久，但排除了Idea的问题。接着加上 ***-X*** 命令，用于显示debugInfo.

{% highlight java %}
mvn -X archetype:generate -DgroupId=com.9leg.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
{% endhighlight %}

输出信息如下：

{% highlight java %}
[INFO] Generating project in Batch mode
[DEBUG] Searching for remote catalog: http://repo1.maven.org/maven2/archetype-catalog.xml
{% endhighlight %}
看来是请求网络上的catalog.xml文件，才导致速度很慢。直接复制了url用浏览器打开，速度也是超级慢，等了很久才打开。
看来问题就是出现在这里。

### 问题解决
1.	直接下载archetype-catalog.xml文件，放到本地的apache-maven目录中。
2.	在使用mvn archetype:generate命令时，加上-DarchetypeCatalog=local,以替换网络上的catalog.xml。

### 题外话
我切换到eclipse，生成maven项目并且选用了骨架archetype，没有任何问题。在eclipse中可以设置maven的archetype catalog，不管是本地的，还是远程的catalog，都可以设置。
切换到Idea中，找了半天，搜索了半天，似乎没有这个设置。只是可以在创建项目时，单独的设置自定义的archetype，而不能设置整个archetype列表。有谁知道请告知，谢谢。

参考:[ARCHETYPE-202](http://jira.codehaus.org/browse/ARCHETYPE-202){:target="_blank"}

原创文章转载请注明出处: [用mvn骨架生成项目速度慢的问题](http://9leg.com/maven/2015/02/01/why-is-mvn-archetype-generate-so-low.html)
