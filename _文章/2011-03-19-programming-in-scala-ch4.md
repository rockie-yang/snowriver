---
ID: 384
post_title: >
  Programming in Scala 学习笔记ch4
  Classes and objects
author: riv
post_date: 2011-03-19 13:22:33
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/19/programming-in-scala-ch4/
published: true
---
在Scala世界里，所有东西都是对象。这就让我有些担心了，可能耗费资源就会比较多。后面这段解释打消我的疑惑。
<pre class="brush:scala">
class ChecksumAccumulator {
    var sum = 0
}
</pre>
如果定义了两个对象acc和csa。那么内存结构就是下面这样的。两个对象里面的变量指向同一个地方。正因为这个原因使用immutable对象应该也不会使资源耗费增加多少。
<a href="http://snowriver.org/blog/wp-content/uploads/2011/03/ScalaShareObject.png"><img src="http://snowriver.org/blog/wp-content/uploads/2011/03/ScalaShareObject-300x154.png" alt="" title="ScalaShareObject" width="300" height="154" class="aligncenter size-medium wp-image-386" /></a>
如果其中一个实例的值变了。那么内存结构就变了。
<pre class="brush:scala">
    acc.sum = 3
</pre>
<a href="http://snowriver.org/blog/wp-content/uploads/2011/03/ScalaDiffObject.png"><img src="http://snowriver.org/blog/wp-content/uploads/2011/03/ScalaDiffObject-300x157.png" alt="" title="ScalaDiffObject" width="300" height="157" class="aligncenter size-medium wp-image-390" /></a><!--more-->
从上面例子中看到如果没有定义成员的访问级别，那么外部就可以访问成员。scala也可以定义private成员，那么只有内部才可以访问。有些函数定义也可以用更简单的方式来写。
<pre class="brush:scala">
class ChecksumAccumulator {
    // 定义为private那么只有内部才能访问
    private var sum = 0

    // 有些简单函数可以只用一个表达式来表示。这个函数就可以用
    // def add(b: Byte): Unit = sum += b
    // 这个函数实际上只是改变本身的状态而不返回任何值。也可写为
    // def add(b: Byte) {sum += b}
    def add(b: Byte): Unit = {
        // b是一个常量。不能做b＝1之类的操作
        // 返回类型Unit相当于void
        sum += b
    }

    // 有些简单函数可以只用一个表达式来表示。这个函数就可以用
    // def checksum(): Int = ~(sum & 0xFF) + 1
    def checksum(): Int = {
        // return语句不是必须的，scala总是返回最后一个语句的值
        // 可以直接写
        // ~(sum & 0xFF) + 1
        return ~(sum & 0xFF) + 1
    }
}
</pre>
Scala里不能有静态函数。Scala里用来取代的是singleton。JDK里也不知道为什么不提供singleton的实现，每次还得自己写一个。定义一个singleton很简单，用object关键字代替class关键字就可以了。用起来和Java的静态函数一模一样。
<pre class="brush:scala">
object ASingleton{
    var a = 0
}
ASingle.a = 1
</pre>