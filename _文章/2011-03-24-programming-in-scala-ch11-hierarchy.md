---
ID: 474
post_title: >
  Programming in Scala 学习笔记ch11
  hierarchy
author: riv
post_date: 2011-03-24 21:52:29
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/24/programming-in-scala-ch11-hierarchy/
published: true
---
<a href="http://snowriver.org/blog/wp-content/uploads/2011/03/scala_hierarchy.png"><img src="http://snowriver.org/blog/wp-content/uploads/2011/03/scala_hierarchy.png" alt="" title="scala_hierarchy" width="530" height="360" class="aligncenter size-full wp-image-482" /></a>
Any是scala结构的最顶层，相当于Java的Object。但是这个类是没有实现的，从scala的库里是找不到的。甚至在API文档里都没有（2.7.6版API文档里有描述http://www.scala-lang.org/api/2.7.6/）。应该是编译器做了什么手脚，让所有的类都继承自Any。

在Any下方有两个类AnyVal和AnyRef。这两个类在API有描述，但是也是没有代码的。AnyVal是所有值类的基类，而AnyRef是所有对象类的基类。

如果想找基本值类的代码，如Int，Long等，那也是没有的。所以这儿的说明只是利于理解，这些类都是由编译器来处理的。

所有类层次结构的末端，还有两个特殊的类Null和Nothing。Null是所有对象类的子类，用于实现类似Java里的null功能。Nothing是所有类的子类，一个用途就是表示函数不会正常返回。