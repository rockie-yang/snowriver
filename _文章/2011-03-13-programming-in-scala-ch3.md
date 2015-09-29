---
ID: 378
post_title: >
  Programming in Scala 学习笔记ch3
  Next step in scala
author: riv
post_date: 2011-03-13 22:50:19
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/13/programming-in-scala-ch3/
published: true
---
下面这段小程序用起来感觉不错。这样使用for循环一般很难出错，不象使用<、<=、>、>=，很容易把边界给搞错了。而这背后0 to 1居然隐藏这一切皆是对象这个事实。0是对象，1是对象。0 to 1其实就是调用对象0的to函数，返回值就是一个Range对象。
<pre class="brush:scala">
object EverythingIsObject {
    def main(args: Array[String]): Unit = {
    	// 定义一个变量，是一个数组
    	// 数组的类型已经知道了，就不用在多此一举了
        var ar = Array("Hello", ",", "World")
        for (i <- 0 to 1)
            println(ar(i))
    }
}
</pre><!--more-->

下面这个程序是对List的基本使用。List在下面的例子当中是不可变的。我对这样的性能还不是很有把握，每次都生成一个新的对象在对象小的时候应该没问题，大的时候就不好说了。书上说22章和24章会有专门对可变List的解释，就到那时候再看咯。
<pre class="brush:scala">
object ListIsImmutable {
    def main(args: Array[String]): Unit = {
        // 定义List
        val l1 = List(1, 2)
        val l2 = List(3, 4)

        // l1和l2连起来赋给l3
        // 原来的l1和l2不变，l3是一个新对象
        // 所有有:的操作符是应用在右边的，也就是相当于l2.:::l1
        val l3 = l1 ::: l2
        println(l3)

        // 调用l2.::1，这个操作返回一个新的List. l2本身不变
        // 这个List保护1和l2
        val l4 = 1 :: l2
        println(l4)

        // Nil是一个空的List
        // 应该相当于Nil.::3.::2.::1
        // 生成List(1,2,3)
        val l5 = 1 :: 2 :: 3 :: Nil
        println(l5）
    }
}
</pre>

JAVA中最让我用的最不爽的就是没有Tuple。JDK里就连个Pair都没有，还让我自己写。下面的例子是如何使用tuple。
<pre class="brush:scala">
object UseTuple {
  def main(args : Array[String]) : Unit = {
	  // 定义tuple
	  // JAVA中没有tuple是很让我奇怪的一件事
	  val t = (1, 2, 3)
	  // 第一个元素，
	  // TODO 为什么用_1这种方式没有搞明白
	  println(t._1)
	  
	  println(t)
	  
	  // tuple可以有不同类型
	  val t2 = (1, "string", 2.3)
	  println(t2)
  }
}
</pre>

下面例子是使用Set和Map的例子。在scala里，Collection分为两类mutable和immutable。mutable很好理解，就是可以改变的。而immutable对象在创建的时候就确定了。后面对immutable对象的修改操作实际上是生成新的对象。就像一个铜板有两面一样，mutable和immutable也是各有各的好处，要不然也不会搞出两套出来。大部分情况使用mutable就可以了，这也是scala比较推荐的方式。如果需要频繁修改可能需要immutable。
<pre class="brush:scala">
import scala.collection.immutable.HashSet

object UseSetMap {
    def main(args: Array[String]): Unit = {
        // 定义一个Set。Set可以包含不同类型还真是不错
        // 在scala里，默认使用的是immutable的类型
        // 就是每次改变都会产生一个新的对象
        var s = Set("a", 1, "b")

        // 这个s和上面的s已经不是同一个对象了.
        // 不信可以通过单步调试看到对象的id已经变了
        s += 3
        
        println(s)
        println(s.contains(1))
        println(s.contains("b"))
        println(s(1))
        println(s.getClass())

        // 普通的Set不需要import，而HashSet就需要显式的import
        // 而且还需要指定mutable还是immutable的
        var hs = HashSet("b", 3)
        println(hs)
        println(hs.getClass())

        var m = Map(1 -> "a", 2 -> "b", "3" -> "c")
        m += ("4" -> "d")
        println(m)
        println(m(1))
        println(m.getClass())

    }
}
</pre>

