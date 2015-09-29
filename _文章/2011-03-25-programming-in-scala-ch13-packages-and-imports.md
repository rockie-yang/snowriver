---
ID: 511
post_title: >
  Programming in Scala 学习笔记ch13
  packages and imports
author: riv
post_date: 2011-03-25 22:32:35
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/25/programming-in-scala-ch13-packages-and-imports/
published: true
---
scala包格式比Java多了一种。用大括号括起来的那种，这样可以在一个文件里嵌套定义多个包。但是在一个文件里定义多个包，到底有什么好处呢？

scala不仅可以在文件开头导入，还可以在其它任何地方导入。这种方式可以说是按需定义。和所有导入都定义在顶端方式可以说是各有优缺点。定义在顶端可以直接知道这个文件依赖于哪些外部元素（当然直接用绝对路径还是无法知晓）。而随时定义，可以知道导入的东西在哪儿使用的。

除了导入class，object，trait外。还可以导入具体某一个函数，某一个成员等等。

如果有些导入会导致名字冲突。定义一个别名就是解决名字冲突最好的方式。
<pre class="brush:scala">
import java.{sql => S}
// 然后java.sql可以用S代替了
</pre>

大部分内容需要我们自己手工导入。而有三个包的内容会自动导入，不需要程序显式再导入。java.lang、scala、Predef。

一个类可以有一个伴对象（companion object）。一个对象也可以有一个伴类（companion class）。它们之间的几乎是透明的，不太受访问权限的控制。

和Java不同，scala可以在包范围内直接定义函数，变量。任何可以放在类里的定义可以放在包内定义。