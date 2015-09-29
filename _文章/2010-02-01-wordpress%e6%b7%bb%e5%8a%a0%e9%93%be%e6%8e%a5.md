---
ID: 31
post_title: WordPress添加链接
author: riv
post_date: 2010-02-01 13:49:49
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2010/02/01/wordpress%e6%b7%bb%e5%8a%a0%e9%93%be%e6%8e%a5/
published: true
---
在Links中添加一个Catergory后还要再Appearance-&gt;Widgets将Links拖到右边的Sidebar中。

为头上添加一个链接album。修改Appearance-&gt;Editor-&gt;header.php文件。

在
<pre class="brush: xml;">
&lt;?php wp_list_pages(‘title_li=’); ?&gt;
</pre>
的下面加上
<pre class="brush: xml;">
&lt;li&gt;
&lt;a href=”http://snowriver.org/album/” title=”Album”&gt;Album&lt;/a&gt;
&lt;/li&gt;
</pre>

这种方法有个缺点，换主题或升级主题后需要重新编辑。现在还不知道有什么其它方法。