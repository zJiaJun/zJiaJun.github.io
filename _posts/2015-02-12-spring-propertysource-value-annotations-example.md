---
layout: post
title: Spring的@PropertySource和@Value注解例子
date: 2015-02-12 15:21:52
category: "spring"
---

在这篇文章中，我们会利用Spring的**@PropertySource**和**@Value**两个注解从配置文件properties中读取值，以及如何从配置文件中的值转换为List对象。

### 创建Spring配置Class

{% highlight java %}

@Configurable
@ComponentScan(basePackages = "com.9leg.java.spring")
@PropertySource(value = "classpath:spring/config.properties")
public class AppConfigTest {
    
    @Bean
    public PropertySourcesPlaceholderConfigurer propertyConfigInDev() {
        return new PropertySourcesPlaceholderConfigurer();
    }
    
}
{% endhighlight %}

通过**@PropertySource**注解将properties配置文件中的值存储到Spring的
**Environment**中，Environment接口提供方法去读取配置文件中的值，参数是properties文件中定义的key值。上面是读取一个配置文件，如果你想要读取多个配置文件，请看下面代码片段：
{% highlight java %}
@PropertySource(value = {"classpath:spring/config.properties","classpath:spring/news.properties"})
{% endhighlight %}

在Spring 4版本中，Spring提供了一个新的注解——**@PropertySources**,从名字就可以猜测到它是为多配置文件而准备的。
{% highlight java %}
@PropertySources({
@PropertySource("classpath:config.properties"),
@PropertySource("classpath:db.properties")
})
public class AppConfig {
	//something
}
{% endhighlight %}

另外在Spring 4版本中，**@PropertySource**允许忽略不存在的配置文件。先看下面的代码片段：

{% highlight java %}
@Configuration
@PropertySource("classpath:missing.properties")
public class AppConfig {
	//something
}
{% endhighlight %}
如果missing.properties不存在或找不到,系统则会抛出异常**FileNotFoundException**。

{% highlight java %}
Caused by: java.io.FileNotFoundException: 
		class path resource [missiong.properties] cannot be opened because it does not exist
{% endhighlight %}

幸好Spring 4为我们提供了**ignoreResourceNotFound**属性来忽略找不到的文件

{% highlight java %}
@Configuration
	@PropertySource(value="classpath:missing.properties", ignoreResourceNotFound=true)
	public class AppConfig {
		//something
	}
{% endhighlight %}

{% highlight java %}
  @PropertySources({
		@PropertySource(value = "classpath:missing.properties", ignoreResourceNotFound=true),
		@PropertySource("classpath:config.properties")
        })
{% endhighlight %}

最上面的AppConfigTest的配置代码等于如下的XML配置文件

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
    http://www.springframework.org/schema/context   http://www.springframework.org/schema/context/spring-context-4.0.xsd">
 
    <context:component-scan base-package="com.9leg.java.spring"/>
 
    <bean class="org.springframework.context.support.PropertySourcesPlaceholderConfigurer">
        <property name="ignoreUnresolvablePlaceholders" value="true"/>
        <property name="locations">
          <list>
            <value>classpath:spring/config.properties</value>
          </list>
        </property>
      </bean>
</beans>
{% endhighlight %}

### 创建properties配置文件

{% highlight java %}
server.name=9leg,spring
server.id=10,11,12
server.jdbc=com.mysql.jdbc.Driver
{% endhighlight %}

### 创建一个简单的服务

{% highlight java %}
@Component(value = "mockConfigTest")
public class MockConfigTest {

    @Value("#{'${server.name}'.split(',')}")
    private List<String> servers;

    @Value("#{'${server.id}'.split(',')}")
    private List<Integer> serverId;
    
    @Value("${server.host:127.0.0.1}")
    private String noProKey;
    
    @Autowired
    private Environment environment;
    
    public void readValues() {
        System.out.println("Services Size : " + servers.size());
        for(String s : servers)
            System.out.println(s);
        System.out.println("ServicesId Size : " + serverId.size());
        for(Integer i : serverId)
            System.out.println(i);
        System.out.println("Server Host : " + noProKey);
        String property = environment.getProperty("server.jdbc");
        System.out.println("Server Jdbc : " + property);        
    }

    public static void main(String[] args) {
        AnnotationConfigApplicationContext annotationConfigApplicationContext = new AnnotationConfigApplicationContext(AppConfigTest.class);
        MockConfigTest mockConfigTest = (MockConfigTest) annotationConfigApplicationContext.getBean("mockConfigTest");
        mockConfigTest.readValues();
    }
}
{% endhighlight %}
首先使用**@Component**注解声明，接着就是属性字段上的**@Value**注解，但在这里比较特殊，是通过**split()**方法，将配置文件中的9leg，spring分割后组成list对象。心细的同学可能注意到了**server.host**这个key并不存在配置文件中。是的,在这里使用的是默认值，即127.0.0.1,它的格式是这样的。

{% highlight java %}
@value("${key:default}")
private String var;
{% endhighlight %}

然后注入了**Environment**,可以通过**getProperty(key)**来获取配置文件中的值。
运行main方法，来看下输出结果：

{% highlight java %}
Services Size : 2
9leg
spring
ServicesId Size : 3
10
11
12
Server Host : 127.0.0.1
Server Jdbc : com.mysql.jdbc.Driver
{% endhighlight %}

最后要说一点，在main方法中请使用**new AnnotationConfigApplicationContext(AppConfigTest.class)**来代替**new ClassPathXmlApplicationContext("applicationContext.xml")**或者**new FileSystemXmlApplicationContext("src/main/resources/applicationContext.xml")**。

原创文章转载请注明出处: [Spring的@PropertySource和@Value注解例子](http://9leg.com/spring/2015/02/12/spring-propertysource-value-annotations-example.html)
