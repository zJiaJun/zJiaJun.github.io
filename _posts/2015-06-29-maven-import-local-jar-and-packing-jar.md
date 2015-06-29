---
layout: post
title: Maven引入本地jar包并生成jar包运行修改MANIFEST.MF文件
date: 2015-06-29 16:23:56
category: "maven"
---

由于项目需要发送短信的功能，确定了产品后，开始开发，发现第三方提供的jar没有maven坐标。于是就开启了一系列的坑爹之路，最后还是解决了，纪录下。

先大致介绍下项目环境，以便能够更好的理解。
首先项目是分多模块的，3个jar包，1个war包。其中2个jar包是任务运行，批处理，监控等，发短信的功能就在其中一个jar中完成。还有个jar是core包，一些通用的公用的类，配置文件，services服务等。war包就是个服务接口，利用SpringMVC完成。

以下所有的修改都在一个任务jar中。

从第三方下载的jar复制到src/main/resources/lib目录下(新建lib目录),

引用方式:

{% highlight java %}
<dependency>
	<groupId>SMS_SDK_JAVA</groupId>
	<artifactId>SMS_SDK_JAVA</artifactId>
	<version>v2.6.3r</version>
	<scope>system</scope>
	<systemPath>${project.basedir}/src/main/resources/lib/SMS_SDK_JAVA_v2.6.3r.jar</systemPath>
</dependency>
{% endhighlight %}

这里的scope只能是system范围,systemPath属性指定jar的路径。

看下原本的打包方式:
{% highlight java %}

<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-jar-plugin</artifactId>
	<version>2.3.2</version>
	<configuration>
		<archive>
			<manifest>
				<-- 在jar包的MF文件中生成Class-Path属性 -->
				<addClasspath>true</addClasspath>
				<-- Class-Path 前缀 -->
				<classpathPrefix>lib/</classpathPrefix>
				<-- main全限定包名 -->
				<mainClass>xxx.MainTask</mainClass>
			</manifest>
		</archive>
	</configuration>
</plugin>

<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-assembly-plugin</artifactId>
	<version>2.3</version>
	<configuration>
		<descriptors>
			<descriptor>src/main/resources/assembly.xml</descriptor>
		</descriptors>
	</configuration>
	<executions>
		<execution>
			<id>make-assembly</id>
			<phase>package</phase>
			<goals>
				<goal>single</goal>
			</goals>
		</execution>
	</executions>
</plugin>

{% endhighlight %}

assembly.xml就不贴出来了，主要工作就是打tar.gz包。
打出来的jar包中,并不包含system范围的jar包，就是第三方的包。并且在jar包的MF文件的classpath也未找到第三方的lib路径。

最后修改如下:
{% highlight java %}

<configuration>
	<archive>
		<manifest>
			<addClasspath>true</addClasspath>
			<classpathPrefix>lib/</classpathPrefix>
			<mainClass>com.madhouse.platform.maddsp.task.MainTask</mainClass>
		</manifest>
		<-- 添加classpath缺少的内容-->
		<manifestEntries>
			<Class-Path>lib/SMS_SDK_JAVA_v2.6.3r.jar</Class-Path>
		</manifestEntries>
	</archive>
</configuration>
{% endhighlight %}

这样第三方的jar包就包含在classpath中，运行的java -jar xxx.jar的时候，也就不会报NoClassFound错误了。

完整的MF内容如下：

{% highlight java %}
Manifest-Version: 1.0
Archiver-Version: Plexus Archiver
Created-By: Apache Maven
Built-By: zhujiajun
Build-Jdk: 1.7.0_72
Main-Class: xxx.MainTask
Class-Path: lib/SMS_SDK_JAVA_v2.6.3r.jar lib/org.apache.oltu.oauth2.resourceserver-1.0.0.jar l
 ib/org.apache.oltu.oauth2.common-1.0.0.jar lib/json-20131018.jar lib/
 org.apache.oltu.oauth2.resourceserver-filter-1.0.0.jar lib/org.apache
 .oltu.oauth2.client-1.0.0.jar lib/druid-1.0.14.jar lib/ehcache-core-2
 .6.10.jar lib/spring-websocket-4.1.2.RELEASE.jar lib/spring-core-4.1.
 2.RELEASE.jar lib/spring-messaging-4.1.2.RELEASE.jar lib/spring-beans
 -4.1.2.RELEASE.jar lib/mail-1.4.4.jar lib/activation-1.1.jar lib/slf4
 j-api-1.7.5.jar lib/logback-classic-1.0.11.jar lib/logback-core-1.0.1
 1.jar lib/poi-3.11.jar lib/commons-codec-1.9.jar lib/poi-ooxml-3.11.j
 ar lib/poi-ooxml-schemas-3.11.jar lib/xmlbeans-2.6.0.jar lib/stax-api
 -1.0.1.jar lib/junit-4.10.jar lib/hamcrest-core-1.1.jar lib/spring-te
 st-4.1.2.RELEASE.jar lib/fastjson-1.1.37.jar lib/commons-fileupload-1
 .3.1.jar lib/commons-io-2.2.jar lib/httpmime-4.3.5.jar lib/httpclient
 -4.3.5.jar lib/httpcore-4.3.2.jar lib/commons-logging-1.1.3.jar lib/j
 stl-1.2.jar lib/jedis-2.6.0.jar lib/commons-pool2-2.0.jar lib/mybatis
 -3.2.8.jar lib/mybatis-spring-1.2.2.jar lib/mysql-connector-java-5.1.
 31.jar lib/spring-web-4.1.2.RELEASE.jar lib/spring-aop-4.1.2.RELEASE.
 jar lib/aopalliance-1.0.jar lib/spring-webmvc-4.1.2.RELEASE.jar lib/s
 pring-expression-4.1.2.RELEASE.jar lib/spring-context-4.1.2.RELEASE.j
 ar lib/spring-context-support-4.1.2.RELEASE.jar lib/spring-jdbc-4.1.2
 .RELEASE.jar lib/spring-aspects-4.1.2.RELEASE.jar lib/aspectjweaver-1
 .8.3.jar lib/spring-tx-4.1.2.RELEASE.jar lib/spring-data-redis-1.4.2.
 RELEASE.jar
{% endhighlight %}

另外如果有需要可以定制MF文件,参考[Apache Maven manifestEntries官网说明](http://maven.apache.org/shared-archives/maven-archiver-LATEST/examples/manifestEntries.html){:target="_blank"}
和[IBM Apache Maven您不知道的5件事](http://www.ibm.com/developerworks/cn/java/j-5things13/){:target="_blank"}


原创文章转载请注明出处: [Maven引入本地jar包并生成jar包运行](http://9leg.com/maven/2015/06/29/maven-import-local-jar-and-packing-jar.html)
