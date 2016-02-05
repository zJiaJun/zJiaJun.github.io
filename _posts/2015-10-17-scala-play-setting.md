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
先打个预防针，可能说的会比较啰嗦，是为了把整个过程说清楚，有不明白或疑问的,请都留下你的评论，我会一一回复。

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
可以使用sbt -h,测试是否配置的生效了。


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

最后需要给bin目录下的sbt赋予可执行权限,chmod u+x sbt。
到这一步，在终端使用sbt相关命令的时候，都会使用指定的目录。开发的时候，还是要依赖IDE，后面就说明如何在IDEA中也使用指定的目录。

首先，打开IDEA的Default Setting(注意不是"cmd+,",Default Setting是全局默认设置，而另外个For current project,
以后就不必为每个项目都设置一遍了),搜索SBT,SBT设置页面最下面默认设的是Bundled,我们选择Custom，指定前面解压出来的bin目录下的sbt-launch.jar。

由于IDEA是通过sbt-launch.jar来操作sbt的，并不是sbt命令。所以前面修改的目录不管用，还是会在用户目录下自动创建.sbt和.ivy2文件夹。
我们把sbt-launch.jar解压出来，看看到底是什么造成的。jar包里有编译好的class文件，其中在sbt目录下有个sbt.boot.properties文件,这就是我们需要修改的，
先看下部分的配置信息:

{% highlight java %}
[repositories]
  local
  typesafe-ivy-releases: https://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly
  maven-central

[boot]
  directory: ${sbt.boot.directory-${sbt.global.base-${user.home}/.sbt}/boot/}

[ivy]
  ivy-home: ${sbt.ivy.home-${user.home}/.ivy2/}
  checksums: ${sbt.checksums-sha1,md5}
  override-build-repos: ${sbt.override.build.repos-false}
  repository-config: ${sbt.repository.config-${sbt.global.base-${user.home}/.sbt}/repositories}
{% endhighlight %}

repositories是远程仓库地址，boot就是设置.sbt目录，ivy设置.ivy2仓库目录,最后有个repository-config，我们可以在修改后的sbt目录下新建
repositories文件，填写访问速度快的仓库地址，会覆盖默认的repositories设置信息。另外sbt.boot.directory，sbt.ivy.home，sbt.repository.config
这些都是可配置的参数信息，相当于不用修改这配置文件，设置参数就行，但我在IDEA中设置的时候不起作用，使用-Dsbt.boot.directory=/xxx/xxx,
谁知道这种方式设置的请告知。最后修改后的如下:

{% highlight java %}
[repositories]
  local
  typesafe-ivy-releases: https://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly
  maven-central


[boot]
  directory: /Users/zhujiajun/Work/sbt/boot

[ivy]
  ivy-home: /Users/zhujiajun/Work/ivy-repository
  checksums: ${sbt.checksums-sha1,md5}
  override-build-repos: ${sbt.override.build.repos-false}
  repository-config: /Users/zhujiajun/Work/sbt/repositories
{% endhighlight %}

- 2016.02.05更新开始

针对在IDEA中设置不起作用的问题,今天又查了下文档,终于解决了.不知道和IDEA升级到15版本是否有关系,因为我记得之前的14系列版本,也是这样设置的,然而不起作用,
也有可能是我记错了.
在IDEA的设置菜单中Build Tools下的sbt,我设置了Launcher(sbt-launcher.jar)为Custom的,就是指定修改后的sbt-launcher.jar,然而在refresh sbt项目
的时候,IDEA会出现警告如下:

{% highlight java %}
15:08:53 Resolver Indexer: Repository is absent or invalid: /Users/zhujiajun/.ivy2/cache
{% endhighlight %}
很明显,说默认的仓库地址不存在或者无效的,然而Custom指定的sbt-launcher.jar中的sbt.boot.properties文件已经修改过ivy-home了,没起作用?
我猜测是IDEA装了scala语言插件有关系,还是使用默认的ivy仓库地址.
其实要解决也很简单,还是在Build Tools下的sbt设置,有一个JVM Options,可以设置堆大小,和其他的jvm参数(VM parameters),在这里设置就行了

{% highlight java %}

-XX:MaxPermSize=384M //这是已有的默认值
-Dsbt.ivy.home=/Users/zhujiajun/Work/ivy-repository //新加
-Dsbt.boot.directory=/Users/zhujiajun/Work/sbt/boot //新加

{% endhighlight %}

再refresh下项目,搞定.PS:在sbt设置的时候,建议勾上Download Sources选择,这样jar包sources会自动下载到ivh仓库里.否则当你打开源码包类文件的时候,
根据IDEA提示再去下载sources,此时并不会下载在ivy仓库中,而会下载至用户目录下的.ideaLibSources/文件中.

- 2016.02.05更新结束

下面给出repositories文件内容，配置了OSC China的国内仓库，会优先去这里下载，没有的话去官方仓库下载，在下载jar的时候发现，大多数jar包会去官方
仓库下载，一些常用的可以在OSC上下载到，毕竟OSC是maven仓库地址。

{% highlight java %}
[repositories]
local
oschina nexus:http://maven.oschina.net/content/groups/public/
typesafe: http://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly
maven-central
sbt-plugins-repo: http://repo.scala-sbt.org/scalasbt/sbt-plugin-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext]
play: http://private-repo.typesafe.com/typesafe/maven-releases/
sonatype-snapshots: https://oss.sonatype.org/content/repositories/snapshots
typesafe-releases: https://repo.typesafe.com/typesafe/releases
typesafe-ivy-releasez: https://repo.typesafe.com/typesafe/ivy-releases, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext]
{% endhighlight %}

最后一步，将修改后的sbt.boot.properties文件更新到jar中，或者根据此配置文件重新打个jar包也可以。现在使用IDEA的时候，也会使用指定的目录了。


sbt设置到此结束。接下来说下基于play framework的设置。要使用play，就要下载typesafe-activator-1.3.6.zip,这是包含很多库的。
同样的操作，进入解压完成的目录，解压activator-launch-1.3.6.jar，修改sbt.boot.properties配置文件，更新jar。

使用./activator ui,可以启动服务，页面会自动打开，地址是http://127.0.0.1:8888/home,有很多模版，可以选择模版后指定想要的目录生成。
现在使用./activator new命令来创建play-scala项目,第一步是根据模版选择，一共有6个模版，如下:

{% highlight java %}
Fetching the latest list of templates...

Browse the list of templates: http://typesafe.com/activator/templates
Choose from these featured templates or enter a template name:
  1) minimal-akka-java-seed
  2) minimal-akka-scala-seed
  3) minimal-java
  4) minimal-scala
  5) play-java
  6) play-scala
(hit tab to see a list of all templates)
>
{% endhighlight %}
输入6，回车，接着输入项目名称,会在当前目录下生成指定的项目。如想要方便的执行activator命令，也需要配置系统变量，
export PATH=/Users/zhujiajun/Work/activator-dist-1.3.6:$PATH，这样就方便在哪个目录都可以执行命令了。

接着需要修改生成的项目，否则使用IDEA导入项目会提示很多错误，我在解决一个后，又出现个，如此循环。
首先修改根目录下的build.sbt文件，修改scalaVersion为2.11.7，默认好像是2.11.6。修改依赖库，将specs2这一行修改为
"org.specs2" %% "specs2" % "2.4.2"，最后注释掉尾部的一句话routesGenerator := InjectedRoutesGenerator，这里要说明下为什么，
这个依赖注入是play2.4的功能[官方说明](https://playframework.com/documentation/2.4.x/JavaDependencyInjection){:target="_blank"}
，由于使用的是2.3.9，所以并不支持，否则导入IDEA的时候又会提示错误信息了。哪为什么不使用2.4.3最新版本呢？因为我使用的是java1.7版本，
play 2.4.3不支持java1.6和1.7了，否则导入IDEA构建的时候，就会提示异常信息，如下:

{% highlight java %}
java.lang.UnsupportedClassVersionError: play/runsupport/classloader/ApplicationClassLoaderProvider : Unsupported major.minor version 52.0
{% endhighlight %}

另附上一段官方说明:"The support for Java 6 and Java 7 was dropped and Play 2.4 now requires Java 8.",[详细官方说明](https://www.playframework.com/documentation/2.4.x/Migration24){:target="_blank"}

build.sbt修改完成.然后进入project目录修改plugins.sbt文件，将第一行的play插件依赖的版本号修改为2.3.9，默认是2.4.3，原因之前已经说明了。

{% highlight java %}
// The Play plugin
addSbtPlugin("com.typesafe.play" % "sbt-plugin" % "2.3.9")
{% endhighlight %}

使用IDEA导入项目，要等很长时间，需要下载很多jar依赖，导入完成后，将Application类修改为object，默认是class的，class是针对paly2.4版本的。

{% highlight scala %}
object Application extends Controller {

  def index = Action {
    Ok(views.html.index("Your new application is ready."))
  }
{% endhighlight %}

最后进入工程目录，执行activator run命令,访问http://localhost:9000/,看到页面成功。


参考:


[http://www.scala-sbt.org/0.13/tutorial/zh-cn/Manual-Installation.html](http://www.scala-sbt.org/0.13/tutorial/zh-cn/Manual-Installation.html){:target="_blank"}
[http://www.scala-sbt.org/0.13/tutorial/zh-cn/Activator-Installation.html](http://www.scala-sbt.org/0.13/tutorial/zh-cn/Activator-Installation.html){:target="_blank"}
[https://www.playframework.com/documentation/2.4.x/Migration24](https://www.playframework.com/documentation/2.4.x/Migration24){:target="_blank"}
[https://playframework.com/documentation/2.4.x/JavaDependencyInjection](https://playframework.com/documentation/2.4.x/JavaDependencyInjection){:target="_blank"}

原创文章转载请注明出处：[基于play-scala的sbt目录和ivy仓库设置](http://9leg.com/scala/2015/10/17/scala-play-setting.html)
