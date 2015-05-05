---
layout: post
title: 用Java生成ASCII艺术字
date: 2015-01-20 16:37:54
category: "java"
---

这是一个有趣的java列子，先来看下效果吧：

{% highlight java %}

                                                                                            
              $$$$$$$       $$$$                                                                    
             $$$$$$$$$$     $$$$                                                                    
            $$$$$$$$$$$$    $$$$                                                                    
            $$$$$  $$$$$    $$$$                                                                    
           $$$$$    $$$$$   $$$$        $$            $$                                            
           $$$$$    $$$$$   $$$$     $$$$$$$$      $$$$$$$$$$$                                      
           $$$$$    $$$$$   $$$$    $$$$$$$$$$    $$$$$$$$$$$$                                      
           $$$$$    $$$$$   $$$$   $$$$$$$$$$$   $$$$$$$$$$$$$                                      
           $$$$$$  $$$$$$   $$$$   $$$$$  $$$$$  $$$$$   $$$$$                                      
            $$$$$$$$$$$$$   $$$$   $$$$    $$$$  $$$$$    $$$$                                      
            $$$$$$$$$$$$$   $$$$   $$$$$$$$$$$$  $$$$     $$$$                                      
              $$$$$$$$$$$   $$$$   $$$$$$$$$$$$  $$$$     $$$$                                      
                    $$$$$   $$$$   $$$$$$$$$$$$  $$$$     $$$$                                      
                    $$$$    $$$$   $$$$$         $$$$$   $$$$$                                      
                   $$$$$    $$$$   $$$$$         $$$$$$ $$$$$$                                      
            $$$  $$$$$$     $$$$   $$$$$$$$$$$$  $$$$$$$$$$$$$                                      
            $$$$$$$$$$$     $$$$    $$$$$$$$$$$   $$$$$$$$$$$$                                      
            $$$$$$$$$       $$$$     $$$$$$$$$$    $$$$$$ $$$$                                      
             $$$$$$                     $$$$             $$$$$                                      
                                                  $$    $$$$$$                                      
                                                  $$$$$$$$$$$                                       
                                                  $$$$$$$$$$$                                       
                                                  $$$$$$$$$                                         

{% endhighlight %}

实现代码如下：

{% highlight java %}
public class ASCIIArt {

  public static void main(String[] args) throws IOException {
		 
    int width = 100;
	int height = 30;
		 
    //BufferedImage image = ImageIO.read(new File("/users/zhujiajun/ascii-art.png"));
		    
	BufferedImage image = new BufferedImage(width, height, BufferedImage.TYPE_INT_RGB);
	Graphics g = image.getGraphics();
	g.setFont(new Font("SansSerif", Font.BOLD, 24));
		 
	Graphics2D graphics = (Graphics2D) g;
    graphics.setRenderingHint(RenderingHints.KEY_TEXT_ANTIALIASING,
						RenderingHints.VALUE_TEXT_ANTIALIAS_ON);
    graphics.drawString("9leg", 10, 20);
		 
	//save this image
	//ImageIO.write(image, "png", new File("/users/zhujiajun/ascii-art.png"));
		 
    for (int y = 0; y < height; y++) {
	    StringBuilder sb = new StringBuilder();
		for (int x = 0; x < width; x++) {
			sb.append(image.getRGB(x, y) == -16777216 ? " " : "$");
		}
		 
		if (sb.toString().trim().isEmpty()) {
			continue;
		}
		 
		System.out.println(sb);
	}
		 
  }
		 
}

{% endhighlight %}

-16777216这是什么玩意？
其实是颜色代码（256 * 256 * 256），在这个列子中"-16777216"被替换为空的字符串“ ”。可以读取保存的图片，并且打印出rgb color，会发现不同的颜色会有不同的代码。

原创文章转载请注明出处: [用Java生成ASCII艺术字
](http://9leg.com/java/2015/01/20/ascii-art-java-example.html)

[英文原文链接](http://www.mkyong.com/java/ascii-art-java-example/){:target="_blank"}
