---
layout: post
title:  分析ClassLoader工作机制-2
categories: [java]
tags: [java]
---

##### 如何加载Class文件

ClassLoader加载一个class文件到JVM是需要经过的步骤。

- 找到.class文件并把这个文件包含的字节码加载到内存中。

- 字节码验证。

- Class类数据结构分析及相应的内存分配。

- 符号表的链接。

- 初始化,类中静态属性和初始化赋值，以及静态代码块的执行。
<!--more-->

##### 加载字节码到内存
在抽象类ClassLoader中并没有定义如何去加载，如何去找到指定类并把它的字节码加载到内存需要子类去实现，也就是要实现findClass()方法。看下常用的URLClassLoader是如何实现findClass()的，在URLClassLoader中通过一个URLClassPath类帮助取得要加载的class文件字节流，而这个URLClassPath定义了到哪里去找这个class文件，如果找到这个class文件，再读取它的byte字节流，通过调用defineClass()方法来创建类对象。
来看下URLClassLoader的构造函数，如下
{% highlight java %}
    public URLClassLoader(URL[] urls) { 
        super();
        // this is to make the stack depth consistent with 1.1
        SecurityManager security = System.getSecurityManager();
        if (security != null) {
            security.checkCreateClassLoader();
        }
        this.acc = AccessController.getContext();
        ucp = new URLClassPath(urls, acc);        
}
{% endhighlight %}

发现必须要指定一个URL数组才能够创建URLCLassLoader对象，也就是必须要指定这个ClassLoader默认到哪个目录下去查找class文件。
这个URL数组也是创建URLClassPath对象的必要条件，它是以URL的形式来表示ClassPath路径的。
在创建URLClassPath对象是会根据传过来的URL数组中的路径来判断是文件还是jar包，根据路径的不同分别创建FileLoader或者JarLoader，或者使用默认的加载器。当JVM调用findClass时由这几个加载器来将class文件的字节码加载到内存中。

下表是每个ClassLoader的搜索路径参数：

| ClassLoader类型 | 参数选项 | 说明 |
|-|-|
|BootstrapClassLoader|-Xbootclasspath: <br>-Xbootclasspath/a:<br>-Xbootclasspath/p:|设置BootstrapClassLoader的搜索路径<br>把路径添加到已存在BootstrapClassLoader搜索路径的后面<br>把路径添加到已存在BootstrapClassLoader搜索路径的前面|
|ExtClassLoader|-Djava.ext.dirs=| 设置ExtClassLoader的搜索路径|
|AppClassLoader|-Djava.class.path= <br> -classpath 或 -cp | 设置AppClassLoader的搜索路径|

##### 验证与解析
- 字节码验证，类装入器对于类的字节码要做许多检测，以确保格式正确，行为正确。

- 类准备，在这个阶段准备代表每个类中定义的字段、方法和实现接口所必需的数据结构。

- 解析，在这个阶段类装入器装入类所引用的其他所有类，如超类，接口，字段，方法签名，方法中使用的本地变量。

##### 初始化Class对象
在类中包含的静态初始化器都被执行，在这一阶段末尾静态字段被初始化为默认值。


##### ClassLoader能够完成的事情

- 在自定义路径下查找自定义的class类文件。也许需要的class文件并不总是在设置好的classpath下面，那么就要想办法找到这个类，在这种情况下需要自己实现一个ClassLoader。

- 对要加载的类做特殊出来。如保证通过网络传输类的安全性，可以将类加密后再进行传输，在加载到JVM之前对类的字节码再解密，这个过程就可以放在自定义的ClassLoader中实现。

- 可以定义类的实现机制，如果可以检查已加载的class文件是否被修改，如果修改了，可以重新加载这个类，从而实现热部署。


JVM在加载类之前会检查请求的类是否已经被加载过，也就是调用findLoadedClass()方法查看是否能够返回类的实例。如果类已经加载了，再调用loadClass()方法会导致类冲突，如下异常：
```
Exception in thread "main" java.lang.LinkageError: loader (instance of  com/github/zjiajun/java/core/classloader/PathClassLoader): attempted  duplicate class definition for name: "com/github/zjiajun/java/core/classloader/ClassLoaderInheritance"
```
JVM表示一个类是否是同一个类会有两个条件。一是看这个类的完整类名是否一样，这个类名包括类所在的包名。二是看加载这个类的ClassLoader是否是同一个，这个说的同一个是指ClassLoader的实例是否是同一个实例。即便是同一个ClassLoader类的两个实例，加载同一个类也会不一样。要实现类的热部署可以创建不同的ClassLoader的实例对象，通过不同的实例来加载相同的类。
使用不同的ClassLoader实例加载同一个类，会不会导致JVM的PermGen区无限增大呢？其实并不会，因为ClassLoader对象也会和其他对象一样，当没有对象再引用它以后，会被JVM回收。但需要注意的是，被这个ClassLoader加载的字节码会被保存在PermGen区，这个数据一般只是在执行FullGC时才会被回收的，所以应用在如果大量的动态加载，FullGC又不是太频繁，要注意PermGen区的大小，防止内存溢出。
个人觉得FullGC的次数不应该很频繁，都知道FullGC会导致STW的，对于某些场景下的应用是接收不了的，应该适当的调整MaxPermSize。


