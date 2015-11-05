---
layout: post
title: maven的java版本随jenv切换改变而改变
date: 2015-11-05 21:20:35
category: "maven"
---

在项目中使用的还是java1.7比较多,1.8平时业余项目用用,学习下,毕竟新版本始终会代替旧版本的.在mac上推荐使用jenv工具来管理多java版本,
能够随意切换.


官方网站[http://www.jenv.be/](http://www.jenv.be/){:target="_blank"},还有个是[http://jenv.io/](http://jenv.io/){:target="_blank"}
国人开发的算是升级版本把,能够通过该工具安装java,ant,maven,tomact.对于我来说,使用jenv足够了.


jenv安装和使用就不说了,官方网站已经很详细了.使用jenv切换版本后,java -version是生效了.
但使用mvn -v命令显示的信息一直是最新安装的java版本.应该没有多大问题,但令人很不爽,google下,找到解决方案,如下


在用户目录下新建 ~/.mavenrc文件,内容是:

{% highlight java %}
JAVA_HOME=$(/usr/libexec/java_home -v $(jenv version-name))
{% endhighlight %}

使用了jenv version-name变量,当使用jenv global 1.7或1.8的时候,mvn -v对应的java版本也能切换了.

{% highlight java %}

zhujiajundeMacBook-Pro:Home zhujiajun$ java -version
java version "1.8.0_66"
Java(TM) SE Runtime Environment (build 1.8.0_66-b17)
Java HotSpot(TM) 64-Bit Server VM (build 25.66-b17, mixed mode)

zhujiajundeMacBook-Pro:Home zhujiajun$ mvn -v
Apache Maven 3.3.3 (7994120775791599e205a5524ec3e0dfe41d4a06; 2015-04-22T19:57:37+08:00)
Maven home: /Users/zhujiajun/Work/apache-maven-3.3.3
Java version: 1.8.0_66, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_66.jdk/Contents/Home/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.11.1", arch: "x86_64", family: "mac"

zhujiajundeMacBook-Pro:Home zhujiajun$ jenv global 1.7

zhujiajundeMacBook-Pro:Home zhujiajun$ mvn -v
Apache Maven 3.3.3 (7994120775791599e205a5524ec3e0dfe41d4a06; 2015-04-22T19:57:37+08:00)
Maven home: /Users/zhujiajun/Work/apache-maven-3.3.3
Java version: 1.7.0_72, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.7.0_72.jdk/Contents/Home/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.11.1", arch: "x86_64", family: "mac"

zhujiajundeMacBook-Pro:Home zhujiajun$ java -version
java version "1.7.0_72"
Java(TM) SE Runtime Environment (build 1.7.0_72-b14)
Java HotSpot(TM) 64-Bit Server VM (build 24.72-b04, mixed mode)

{% endhighlight %}

参考:

[https://github.com/gcuisinier/jenv/issues/74](https://github.com/gcuisinier/jenv/issues/74){:target="_blank"}

原创文章转载请注明出处：[maven的java版本随jenv切换改变而改变](http://9leg.com/maven/2015/11/05/maven-java-version .html)
