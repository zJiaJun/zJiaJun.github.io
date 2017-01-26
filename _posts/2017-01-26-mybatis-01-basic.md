---
layout: post
title:  mybatis--01基础概念
date:   2017-01-27 22:23:11
category: "mybatis"
---

mybatis框架一直在用，并没有系统的全面的深入了解，接下来就写几篇文章，梳理下并加上影响。

第一篇是基础概念，大多数文字都是mybatis官方网站文档上的。

- **SqlSessionFactoryBuilder**这个类可以被实例化，使用和丢弃，一旦创建了SqlSessionFactory就不再需要他了。
因此SqlSessionFactoryBuilder实例的**最佳作用域是方法作用域**，也就是局部方法变量。
这个类作用是解析xml文件生成Configuration配置对象，做为构造参数传入DefaultSessionFactory(SqlSessionFactory接口的实现类)

- **SqlSessionFactory**一旦被创建出来就应该在应用的运行期间一直存在，不能对他进行清除或重建。
使用SqlSessionFactory最佳实践是在应用运行期间不要重复创建多次。因此SqlSessionFactory的**最佳作用域是应用作用域**。
有很多方式，最简单的就是使用单例模式。作用是openSession，返回SqlSession操作DB。

- **SqlSession**的实例不是线程安全的，所以每个线程都应该有他自己的SqlSession实例，是不能被共享的，
**最佳作用域是请求或方法作用域**。绝对不能将SqlSession实例的引用放在一个类的静态域，一个类的实例变量。
每次收到Http请求，就打开一个SqlSession，返回响应，就关闭他。关闭操作是很重要的，应该放到finally块中确保每次都能关闭。
通常使用的方式都是，进入方法，调用openSession获取SqlSession，操作DB，关闭，可以通过AOP简化上述流程。

- **映射器实例**(Mapper Instances)是创建用来绑定映射语句的接口，映射器接口的实例是从SqlSession中获得的。
因此从技术层面讲，映射器的最大作用域是和SqlSession相同的，**最佳作用域是方法作用域**。
也就是说，映射器实例应该在调用方法中请求，用过之后就可以废弃。


原创文章转载请注明出处: [mybatis--01基础概念](http://www.9leg.com/mybatis/2017/01/27/mybatis-01-basic.html)

