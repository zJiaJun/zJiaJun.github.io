---
layout: post
title: 初始化Jekyll的markdown文件
date: 2015-01-11 15:17:32
category: "other"
---

自从用了Jekyll，每次写文章前，在正文开始处都要加入一段描述（[FrontMatter](http://jekyllrb.com/docs/frontmatter/){:target="_blank"}）。下面就是这篇文章的FrontMatter。

{% highlight java %}
---
layout: post
title: 初始化Jekyll的markdown文件
date: 2015-01-11 15:17:32
category: "other"
---
{% endhighlight %}

每次不是复制这段内容，就是再敲一遍(如果Mou编辑器可以设置自定义模版多好啊)。这多low啊，逼格去哪里了？请看下面的代码：

{% highlight java %}
public class CreateMDFile {
	
	private static final String USER_HOME = "/Users/zhujiajun";
	
	private static final String POST_DIR = "/Work/_posts";
	
	private static final String MARKDOWN_RULE = "---";
	
	private static final String MARKDOWN_FILE = ".md";
	
	private static final String AUTHOR = "原创文章转载请注明出处: " ;
	
	private static final String WEB_SITE = "http://www.9leg.com/";
	
	private static final String HTML = ".html";
	
	public static void main(String[] args) {
		//default file title
		String fileTitle = "file-title";
		//default post title
		String postTitle = "PostTitle";
		//default post category
		String postCategory = "java";
		//是否转载
		boolean isReprint =  false;
		//customize
		if (args.length > 0 ) {
			fileTitle = args[0];
			postTitle = args[1];
			postCategory = args[2];
			isReprint = args[3].equals("1") ? true : false;
		} 
		
		String userHome = System.getProperties().getProperty("user.home",USER_HOME);
		
		String fileTime = getFormatTime("yyyy-MM-dd");
		// /Users/zhujiajun/Work/_posts
		String fileDir =  userHome + POST_DIR;
		// /2015-01-11-FileTitle.md
		String fileName = File.separator + fileTime + "-" + fileTitle + MARKDOWN_FILE;
		
		File file = new File(fileDir + fileName);
		
		String postTime = getFormatTime("yyyy-MM-dd HH:mm:ss");
		
		PrintWriter pw = null;
		try {
			if (!file.exists()) {
				file.createNewFile();
				pw = new PrintWriter(file,"UTF-8");
				pw.println(MARKDOWN_RULE);
				pw.println("layout: post");
				pw.println("title: " + postTitle);
				pw.println("date: "+ postTime);
				pw.println("category: "+ "\"" + postCategory + "\"");
				pw.println(MARKDOWN_RULE);
				for (int i = 0;i < 10;i++) {
					pw.println();//空行
				}
				pw.println(
						AUTHOR + "[" + postTitle +"]" + 
				"("+ WEB_SITE + postCategory + getFormatTime("/yyyy/MM/dd/") + fileTitle + HTML +")");
				if (isReprint) {
					pw.println();
					pw.println("[转载]()");
				}
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			if (pw != null) {
				pw.close();
			}
		}
	}
	
	static private String getFormatTime(String pattern) {
		Objects.requireNonNull(pattern);
		String formatTime = new SimpleDateFormat(pattern).format(new Date());
		return formatTime;
	}
}
{% endhighlight %}

逼格来了，简单的用java生产文件并写入内容。如果是windows系统需要修改文件路径，我这里的路径是mac系统。对于代码喜欢追求极致，不知道算不算强迫症。代码有什么不妥的地方，请指出。接下来创建个sh脚本方便运行:

{% highlight java%}
echo ---Start create markdown file---
cd workspace/ThinkInJava/bin/
echo 输入文件标题:
read filetitle
echo 输入文章标题:
read posttitle
echo 输入文章分类:
read postcategory
echo 是否转载 是：1 否 ：2
read isReprint

java com.zjiajun.java.markdown.CreateMDFile  ${filetitle} ${posttitle} ${postcategory} ${isReprint}

echo ---End---
{% endhighlight %}

这样就方便多了，运行下sh,自动生成文件头部信息和一些其他的信息，这样就可以更专注内容的写作。


原创文章转载请注明出处: [初始化Jekyll的markdown文件](http://www.9leg.com/other/2015/01/11/create-jekyll-markdown-by-java.html)
