---
layout: post
title:  分析ClassLoader工作机制-1
categories: [java]
tags: [java]
---

classLoader类加载器，作用如下：
- 将class加载到JVM中。

- 检查每个类应该由谁加载，是一种父优先的等级加载机制。

- 将class字节码重新解析成JVM统一要求的对象格式。
<!--more-->

##### ClassLoader常用方法分析
以下是经常会用到的classLoader方法。

|method|return|
|-|-|
|defineClass(byte[], int, int)   | Class<?>    |
|findClass(String)               | Class<?>    |
|loadClass(String)               | Class<?>    |
|resolveClass(Class<?>)          | void        |

- defineClass方法用来将byte字节流解析成JVM能够识别的class对象。有了这个方法意味着不仅仅可以通过class文件实例化对象，还可以通过其他方式实例化对象，如通过网络接收一个类的字节码，拿这个字节码流直接创建类的class对象形式实例化对象。如果直接调用这个方法生成类的class对象，这个类的class对象还没有resolve，这个resolve将会在这个对象真正实例化时进行。

- defineClass通常是和findClass方法一起使用的，通过直接覆盖ClassLoader父类的findClass方法来实现类的加载规则，从而取得要加载类的字节码。然后调用defineClass方法生成类的class对象，如果想在类被加载到JVM中时就被链接，那可以调用resolveClass方法。

- 如果不想重新定义加载类的规则，也没有复杂的处理逻辑，只想在运行时能够加载自己指定的一个类，那么可以用this.getClass().getClassLoader().loadClass("class")
调用ClassLoader的loadClass方法获取这个类的class对象。还有个loadClass(String name, boolean resolve)重载方法，可以决定在什么时候解析这个类。

- ClassLoader是个抽象类，它有很多子类。一般的，如果要实现自己的ClassLoader，都会继承URLClassLoader这个子类，因为这个类已经帮我们实现了大部分工作。

- findClass方法在ClassLoader类中只有方法声明，没有实现，是个模版方法，loadClass方法中会调用findClass方法。

##### ClassLoader的等级加载机制
整个JVM平台提供三层ClassLoader：
- **BootstrapClassLoader**，启动类加载器，加载JVM自身工作需要的类(JAVA_HOME/lib,可以通过-Xbootclasspath选项指定的jar包到内存)，完全由JVM自己控制，需要加载哪个类，怎么加载都由JVM自己控制，别人也访问不到这个类，没有更高一级的父加载器，也没有子加载器。

- **ExtClassLoader**，扩展类加载器(JAVA_HOME/lib/ext,可以通过-Xjava.ext.dirs指定位置中的类库加载到内存,如指定了则原有的ext下的java包不会被引入)，服务的特定目标在System.getProperty("java.ext.dirs")。

- **AppClassLoader**，系统类加载器，它的父加载器是ExtClassLoader，所有在System.getProperty("java.class.path")目录下的类都可以被这个类加载器加载，这个目录就是经常用到的classpath，可以通过-Xjava.class.path或-classpath指定。实现自己的类加载器，它的父加载器都是AppClassLoader。因为不管调用哪个父类构造器，创建的对象都必须最终调用getSystemClassLoader()作为父加载器，而getSystemClassLoader()获取到的正是AppClassLoader。

**很多文章都把BootstrapClassLoader做为ExtClassLoader上一级，其实BootstrapClassLoader并不属于JVM的类等级层次，因为BootstrapClassLoader并没有遵守ClassLoader加载规则，另外BootstrapClassLoader并没有子类，ExtClassLoader的父类也不是BootstrapClassLoader，ExtClassLoader并没有父类，在应用在能提取到的顶层父类是ExtClassLoader。**

ExtClassLoader和AppClassLoader都继承了URLClassLoader类，而URLClassLoader又实现了抽象类ClassLoader。在创建Launcher对象是首先会创建ExtClassLoader，然后将ExtClassLoader对象作为父加载器创建AppClassLoader对象，通过Launcher.getLauncher().getClassLoader()方法获取的ClassLoader就是AppClassLoader对象。所以如果在java应用中没有定向其他ClassLoader，那么除了System.getProperty("java.ext.dirs")目录下的类是由ExtClassLoader加载外，其他类都由AppClassLoader来加载。

JVM加载class文件到内存有两种方式：
- 隐式加载，所谓隐式加载就是不通过在代码里调用ClassLoader来加载所需要的类，而是通过JVM来自动加载需要的类到内存的方式。例如，当我们在类中继承或者引用某个类时，JVM在解析当前这个类时发现引用的类不存在内存中，那么就会自动将这些类加载到内存中。

- 显式加载，在代码中通过调用ClassLoader类来加载一个类的方式。如this.getClass().getClassLoader().loadClass()或者Class.forName()，或者自己实现的的ClassLoader的findClass()方法。

这两种方式是混合使用的。如通过自定义的ClassLoader显式加载一个类时，这个类又引用了其他类，那么这些类就是隐式加载的。

**双亲委托机制**，每个ClassLoader实例都要一个父类加载器的引用。当一个ClassLoader实例需要加载某个类时，它会试图搜索某个类之前，先把这个任务委托给它的父类加载器，这个过程是由上至下依次检查的，首先由BootstrapClassLoader开始加载，如果没加载到，则由ExtClassLoader试图加载，如果还是没有，则由AppClassLoader进行加载，还是没有的话，则返回给委托的发起者，由它来查找并加载类。如果都没有加载到这个类时，则抛出ClassNotFoundException异常。

为什么要使用双亲委托机制呢？
- 避免重复加载，当父类加载了该类的时候，就没有必要子ClassLoader再加载一次。

- 考虑安全因素, 如果不使用委托机制，就可以随时使用自定义的String来动态代替java核心的api中定义的类型，这样会存在安全隐患。















