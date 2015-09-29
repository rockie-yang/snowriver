---
ID: 647
post_title: >
  Spring Integration – XML
  BookOrderProcessing理解
author: riv
post_date: 2011-12-13 22:21:41
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/12/13/spring-integration-xml-bookorderprocessing/
published: true
---
<a href="http://snowriver.org/blog/wp-content/uploads/2011/12/BookOrderProcessingSTS.png"><img src="http://snowriver.org/blog/wp-content/uploads/2011/12/BookOrderProcessingSTS.png" alt="" title="BookOrderProcessingSTS" width="600" height="96" class="alignleft size-medium wp-image-649" /></a>
Spring Integration本身比较简单。但是在STS里的图形显示实在是不好理解。到处都充斥着通道的信息。而这些信息在大部分情况下都没有任何作用。有谁见过ps -ef | grep root，里的｜还要转们突出显示的。下面这个图是从STS里剪的。实在难以理解，还不如直接看xml文件呢。

把该例子的图重新用BPEL/BPMN方式画了一下。至少我自己看起来容易多了。<a href="http://snowriver.org/blog/wp-content/uploads/2011/12/BookOrderProcessing.png"><img src="http://snowriver.org/blog/wp-content/uploads/2011/12/BookOrderProcessing-274x300.png" alt="" title="BookOrderProcessing" width="274" height="300" class="alignright size-medium wp-image-648" /></a>

消息进来之后第一步先根据xpath将消息分成几块。
然后检查根据消息检查是否在库存里面有。
根据是否有做一个条件路由。
有就直接发货。
没有就先转换一下消息格式让供货商能够理解。
然后再给供货商发消息。