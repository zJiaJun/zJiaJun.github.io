---
layout: post
title: 优化Jekyll博客访问慢的问题
date: 2015-01-15 12:32:10
category: "other"
---

博客运行一个多月了，各方面都很满意。唯独国内访问网站速度很慢。

### 多余但必须的废话
平时开着VPN，访问速度倒是不慢，在国内还是很有必要为自己搞一个稳定vpn的，至于原因你们都懂的，除非你肉神翻墙。那么问题来了，哪家vpn稳定且技术强？在这里推荐自己用了很长时间的vpn,不管是看YouTube，还是上google的developer.android.com速度都是刚刚的。
[我的云梯](http://kuaitizi.com/?r=7ffa784e42249e0f){:target="_blank"}，通过这个链接购买的用户，可以优惠10元哦。

### 优化资源文件
回归正题，那怎么优化国内访问速度问题呢？
其实国内访问速度慢，都是请求google相关资源文件导致的，请看下面的html片段：

{% highlight html %}
<link href='http://fonts.googleapis.com/css?family=Spirax' rel='stylesheet' type='text/css'>

<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>
{% endhighlight %}

那么只要请求国内的资源文件就行了，或者你有个人空间，可以直接访问google的资源文件，复制并保存文件，接下来就是引用保存的文件就行了。在这里还是推荐第一种方式，使用现成的**国内前端公共库**像[www.freecdn.cn/](http://www.freecdn.cn/){:target="_blank"},[libs.useso.com/](http://libs.useso.com/){:target="_blank"}，这些公共库都是通过CDN服务优化过的。还有许多，在这里就不一一列举了，有兴趣的朋友可以看下这篇文章[盘点国内网站常用的一些 CDN 公共库加速服务](http://www.cnbeta.com/articles/304469.htm){:target="_blank"}。目前我使用的是360的公共库[libs.useso.com](http://libs.useso.com/){:target="_blank"}，对我来说已经足够了。

360的公共库使用方式很简单，只需简单的把*googleapis*替换为*useso*,如下：

{% highlight html %}
<link href='http://fonts.useso.com/css?family=Spirax' rel='stylesheet' type='text/css'>

<script type="text/javascript" src="http://ajax.useso.com/ajax/libs/jquery/1.8.3/jquery.min.js"></script>
{% endhighlight %}

### 优化统计分析
Jekyll模板默认使用的是googleAnaly网站分析，那么势必会加载google的*analytics.js*，这对于国内未翻墙的用户来说，加载肯定会耗很长的时间，甚至是加载失败，这也是博客访问慢的原因之一。
其实，可以复制并保存一份analytics.js文件，挂载在云存储空间上。
我的做法是屏蔽了googleAnaly分析，使用baidu统计来完成。
修改*_config.yml*文件,设置googleAnaly为false,增加baidu统计配置 :

{% highlight java %}
googleAnaly:
  config: false
  id: UA-12345678-1

baiduTongji:
  config: true
{% endhighlight %}

修改相对应的默认模板,googleAnaly不需要变动，增加baidu统计脚本：
{% highlight html %}

 {percent sign if site.googleAnaly.config percent sign}
        <script>
            (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
            (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
            m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
                })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

            ga('create', 'UA-12345678-1', 'auto');
            ga('send', 'pageview');
        </script>
 {percent sign endif percent sign}
 {percent sign if site.baiduTongji.config percent sign}
        <script>
            var _hmt = _hmt || [];
            (function() {
                var hm = document.createElement("script");
                hm.src = "//hm.baidu.com/hm.js?你的统计标识符";
                var s = document.getElementsByTagName("script")[0]; 
                s.parentNode.insertBefore(hm, s);
            })();
        </script>
{percent sign endif percent sign}
注意：这里使用了percent sign英文单词来代替%，否则会当做判断实际执行。
{% endhighlight %}

最后测试了下，第一次访问请求资源文件保持在1-2秒之间，再次访问保持在400ms－600ms之间，比之前快了许多，可以说是秒开了。

原创文章转载请注明出处: [优化Jekyll博客访问慢的问题](http://www.9leg.com/other/2015/01/15/optimization-of-jekyll-blog-access-slow-problem.html)
