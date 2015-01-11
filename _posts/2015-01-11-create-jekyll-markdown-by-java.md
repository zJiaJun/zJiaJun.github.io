---
layout: post
title: 初始化Jekyll的markdown文件
date: 2015-01-11 15:17:32
category: "other"
---

自从用了Jekyll，每次写文章前，在正文开始处都要加入一段描述（[FrontMatter](http://jekyllrb.com/docs/frontmatter/)）。下面就是这篇文章的FrontMatter。
```
---
layout: post
title: 初始化Jekyll的markdown文件
date: 2015-01-11 15:17:32
category: "other"
---
```
每次不是复制这段内容，就是再敲一遍。这多low啊，逼格去哪里了？请看下面的代码：

{% highlight java %}
public class CreateMDFile {
	
	private static final String USER_HOME = "/Users/zhujiajun";
	
	private static final String POST_DIR = "/Work/_posts";
	
	private static final String MARKDOWN_RULE = "---";
	
	private static final String MARKDOWN_FILE = ".md";
	
	public static void main(String[] args) {
		//default file title
		String fileTitle = "FileTitle";
		//default post title
		String postTitle = "PostTitle";
		//default post category
		String postCategory = "java";
		//customize
		if (args.length == 3 ) {
			fileTitle = args[0];
			postTitle = args[1];
			postCategory = args[2];
		} 
		
		String userHome = System.getProperties().getProperty("user.home",USER_HOME);
		
		String fileTime = getFormatTime("YYYY-MM-DD");
		// /Users/zhujiajun/Work/_posts
		String fileDir =  userHome + POST_DIR;
		// /2015-01-11-FileTitle.md
		String fileName = File.separator + fileTime + "-" + fileTitle + MARKDOWN_FILE;
		
		File file = new File(fileDir + fileName);
		
		String postTime = getFormatTime("YYYY-MM-DD HH:mm:ss");
		
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

逼格来了，简单的用java生产文件并写入内容。如是windows系统需要修改文件路径，我这里的路径是mac系统。对于代码喜欢追求极致，不知道算不算强迫症。上面的代码明显算不上极致，比如main方法入参只有一个，只想定义文件名称，文章标题和分类使用默认，或者反过来，只想定义文章标题和分类。现在明显不满足。其他地方有什么不妥，
请指出。以后只需简单的在终端敲下java命令就行了。

原创文章转载请注明出处: [初始化Jekyll的markdown文件](http://www.9leg.com/other/2015/01/11/create-jekyll-markdown-by-java.html)
