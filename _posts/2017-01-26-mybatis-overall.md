---
layout: post
title:  mybatis--整体架构
categories: [mybatis]
tags: [mybatis]
---

mybatis整体架构分为三层，分别是基础支持层、核心处理层和接口层。
<!--more-->

- 基础支持层
基础支持层包含mybatis的基础模块，这些模块为核心处理层提供支持。

  - 反射模块，封装了java原生的反射操作，提供简介易用的api，方便使用。

  - 类型转换模块，实现JDBC与java类型之前的相互转换，处理参数时，java类型转JDBC类型，处理结果集时，JDBC转java类型。

  - 日志模块，提供日志输出信息，方便调试并定位bug，集成多种日志框架。

  - 资源加载模块，对类加载器进行封装，确定类加载器的使用顺序，并提供了加载类文件和其他资源文件的功能。

  - 解析器模块，提供两个功能，对XPath进行封装，为mybaits初始化时解析配置文件提供支持，另一个功能是为处理动态SQL语句中的占位符提供支持。

  - 数据源模块，简单实现了数据源实现，也可以与第三方法数据源集成。

  - 事物管理模块，简单实现了事物管理，在很多场景中，一般mybatis都与spring框架集成，由spring框架管理事物。

  - 缓存模块，提供了一级和二级缓存，自带这两级缓存与mybatis整个应用是运行在同一个JVM中的，共享同一块内存。所以如果缓存数据量较大，可能会影响系统中其他功能的运行。

  - Binding模块，通过Binding模块将自定义的Mapper接口与映射配置文件关联起来。

- 核心处理层
  - 配置解析，在初始化过程中，会加载mybaits-config.xml配置文件、映射配置文件及Mapper接口中的注解信息，解析后的配置信息会形成相对应的对象并保存到Configuration对象中。

  - SQL解析，解析映射文件中定义的动态SQL节点，并形成数据库可执行的SQL语句。

  - SQL执行，涉及到很多组件，比较重要的是Executor、StatementHandler、ParameterHandler和ResultSetHandler。Executor主要负责维护一级缓存和二级缓存，并提供事物管理的相关操作，它会将数据库相关操作委托给StatementHandler完成。StatementHandler首先通过ParameterHandler完成SQL语句的参数绑定，然后通过java.sql.Statement对象执行SQL语句并得到结果集，最后通过ResultSetHandler完成结果集的映射。

  - 插件，可以添加自定义插件的方式对mybaits进行扩展。

- 接口层，核心就是SqlSession接口，定义了mybatis暴露给应用程序调用的api。
