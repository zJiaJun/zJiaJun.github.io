---
layout: post
title: 基于play-scala的sbt目录和ivy仓库设置
date: 2015-10-17 20:16:15
category: "scala"
---

花了一下午时间，总算全部搞定。时间主要都花费在下载jar包上，虽然开了VPN还是下载慢，没有VPN的话，真心要奔溃的。这期间有太多坑了，所以写这篇文章，
一是记录下，二是方便大家查阅。

### 本人的系统环境
为什么要说系统环境呢？不同的环境有不同的设置方法，但看了这篇文章后，可以举一反三，在其他环境设置也没什么问题。

- OS: OS X EI Capitan 10.11
- IDE: IntelliJ IDEA 14.1.5
- Java Version: 1.7.0_72
- Scala Version: 2.11.7
- SBT Version: 0.13.8
- Typesafe Activator Version: 1.3.6(完整版本,400多MB,包含很多库,不是minimal）
- Play Framework Version: 2.3.9(为什么不用最新的2.4.3,请耐心看完)

### 主要包括的内容

- 修改SBT的目录路径,默认值是user.home下的.sbt文件夹
- 修改ivy的目录仓库地址,默认值是user.home下的.ivy2文件夹
- 修改activator的目录路径,使用和SBT修改后的相同路径,仓库地址同样.这里要说下activator是sbt的超集,
[详细说明请点击](http://www.scala-sbt.org/0.13/tutorial/zh-cn/Activator-Installation.html){:target="_blank"}
- 使用IDEA时，同样使用修改后的sbt路径和ivy仓库地址
- 使用activator ui,activator new,activator run命令
- 使用IDEA构建play-scala的web项目


### 详细说明
先打个预防针，可能说的会比较啰嗦，是为了把整个过程说清楚，有不明白或疑问的或哪里讲的不对,请都留下你的评论，我会一一回复或改正。
将下载的sbt-0.13.8.zip(大约1MB)解压至你的目录,进入sbt-0.13.8目录，可以看见2个文件夹，分别是bin和conf，
显而易见，bin目录下的是可执行文件，conf目录下是配置文件。
用文本工具打开bin目录下的sbt,可以看到如下的声明:

{% highlight java %}
declare -r etc_sbt_opts_file="${sbt_home}/conf/sbtopts"
declare -r win_sbt_opts_file="${sbt_home}/conf/sbtconfig.txt"
{% endhighlight %}

${sbt_home}是系统变量，就像java_home一样,所以要先增加这个变量，否则就算修改了conf目录下的配置文件，也是没有效果的。
打开终端，输入命令 cd ~(进入用户目录，个人习惯修改用户目录下的.bash_profile文件),vi .bash_profile,
新增如下:

{% highlight java %}
SBT_HOME=/Users/zhujiajun/Work/sbt-0.13.8
export PATH=$SBT_HOME/bin:$PATH
{% endhighlight %}

第一行设置sbt_home,第二行可以方便使用sbt命令,当然请填写你的实际地址,保存并退出,最后别忘了source .bash_profile，使修改的文件生效。
接着，就可以修改conf目录下的配置文件了，修改sbtopts文件，文本工具打开并修改,修改3个值就可以,如下:

{% highlight java %}
# Path to global settings/plugins directory (default: ~/.sbt)
#
-sbt-dir  ~/Work/sbt

# Path to shared boot directory (default: ~/.sbt/boot in 0.11 series)
#
-sbt-boot ~/Work/sbt/boot

# Path to local Ivy repository (default: ~/.ivy2)
#
-ivy ~/Work/ivy-repository
{% endhighlight %}

到这一步，在终端使用sbt相关命令的时候，都会使用指定的目录。开发的时候，还是要依然IDE，后面就说明如何在IDEA中也使用指定的目录。
首先，打开IDEA的Default Setting(注意不是"cmd+,",Default Setting是全局默认设置，而另外个For current project,
以后就不必为每个项目都设置一遍了),搜索SBT,SBT设置页面最下面默认设的是Bundled,我们选择Custom，指定前面解压出来的bin目录下的sbt-launch.jar。


未完待续...明天写完




参考:
- [http://www.scala-sbt.org/0.13/tutorial/zh-cn/Manual-Installation.html]{:target="_blank"}
- [http://www.scala-sbt.org/0.13/tutorial/zh-cn/Activator-Installation.html]{:target="_blank"}
- [https://www.playframework.com/documentation/2.4.x/Migration24]{:target="_blank"}
- [https://playframework.com/documentation/2.4.x/JavaDependencyInjection](https://playframework.com/documentation/2.4.x/JavaDependencyInjection){:target="_blank"}

原创文章转载请注明出处：[基于play-scala的sbt目录和ivy仓库设置](http://9leg.com/scala/2015/10/17/scala-play-setting.html)
