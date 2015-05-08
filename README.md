#[9leg.com](http://9leg.com/)


###请使用自己申请的网站统计Id，评论Id等

如今网络是开源的年代，茫茫多的框架。

既然我的博客放在gitHub上，也表示了开源，希望有更多的人来使用Jekyll模版。

但发现很多人Fork我的项目后，并不根据自己情况修改任何配置，而是直接写post了。

这导致我的网站统计和评论带来了不必要的麻烦，简单的说就是，你网站中的评论被我管理，这不是你想要看到的吧。

访问你网站的流量PV和UV等，算在我的统计范围内，这也不是你想要看到的吧(其实我很乐意的...)。

那么就简单的说下，如何个性化自定义配置网站。

**_config.yml**,网站的许多配置都在这个文件中，导航栏信息配置，网站作者信息配置，友链配置，评论和网站统计等。

下面是我的配置：

```
googleAnaly:

  config: false

  id: Your-id-is-here
```

```
baiduTongji:
	
  	config: true
  	
  	id: Your-id-is-here
```

```
disqus:

  config: false
  
  id: Your-id-is-here
```

```
duoshuo:

  config: true
  
  id: Your-id-is-here
```

```
baiduShare:

  config: true
```
可以使用google分析和baidu统计对网站进行流量多方面的信息跟踪查看，在这里使用了baidu统计，**注意：请设置你申请的统计Id**。

在这里不使用google分析的原因是加载ga.js时间过长或者根本无法请求，原因你懂的。

disqus和duoshuo都是网站评论设置，使用了多说，国内还是比较好的，**注意：请设置你申请的多说Id**。

baiduShare是baidu分享插件，比较实用。

最后根据自己的实际情况，修改_layouts下的两个模版html文件，稍微有点html知识都可以看懂并修改。


##重复,请使用自己申请的网站统计Id，评论Id等。

什么？怎么申请？google去...




