---
ID: 438
post_title: >
  Programming in Scala 学习笔记ch9
  control abstraction
author: riv
post_date: 2011-03-21 22:38:55
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/21/programming-in-scala-ch9-control-abstraction/
published: true
---
在编写Java程序时经常写一些非常类似的代码段。譬如说打开一个文件，然后开始操作，这个过程得包在try块里，用完了之后再关闭。这些代码虽然不麻烦，但每次都要写同一段代码总是很烦人了。而且有时候还会忘了写关闭代码，引入了软件潜在的问题。Scala里支持用函数作为参数传给另一个函数，也就是所谓的高阶函数。下面这段代码用于演示对文件的安全操作。对其它类似的场景也可以这么用，譬如说JDBC。
<pre class="brush:scala">
import java.io.FileReader

object SafeFile {
    def main(args: Array[String]): Unit = {
        safeFileReaderOp("./ST.iml", print)
    }

    def print(reader: FileReader) = {
        val i = reader.read
        println(i.toChar)
    }

    // 该函数输入文件名和操作该文件的一个函数
    // 具体怎么操作该文件由输入的函数决定
    // 该函数里可以把文件操作前和操作后的事做掉
    // 这样操作文件就比较方便也更安全
    def safeFileReaderOp(filename: String, 
                         op: FileReader => Unit) = {
        val reader = new FileReader(filename)
        try {
            op(reader)
        }
        finally {
            reader.close()
        }
    }
}
</pre><!--more-->

scala里提供相当多的高阶函数，这使程序编写相当方便。因为虽然应用场合各式各样，但是这些高阶函数抽象几乎不会有任何变化。有不少程序员目前还在不断的开发链表啊，排序啊，忙于边界值测试。实在是最大的浪费。下面程序是对Collection的一些应用。
<pre class="brush:scala">
object CollectionFunctions {
    def main(args: Array[String]): Unit = {
        val l = List(1, 3, 5, 7, -1)

        // list 中是否有>0的数
        val hasNeg = l.exists((n: Int) => n > 0)
        println("has neg " + hasNeg)

        // list中是否有偶数
        val hasOdd = l.exists(_ % 2 == 0)
        println("has odd " + hasOdd)

        // list中大于2的个数
        val largeThen2 = l.count(_ > 2)
        println("count of > 2 " + largeThen2)

        // 把list中大于0的元素拿出来作为另一个list
        val pos = l.filter(_ > 0)
        println("all positive " + pos)

        // 判断是否所有元素均大于0
        val allPos = l.forall(_ > 0)
        println("is all positive " + allPos)

        // 打印每个元素
        l.foreach(println)

        // 所有元素求和
        var sum = 0
        l.foreach(sum += _)
        println("sum is " + sum)

        // 给list里的元素求平方
        val square = l.map((n: Int) => n * n)
        println("squared list " + square)

        // 给里面的元素倒个个
        val reverse = l.reverse
        println("reversed list " + reverse)

        // 排序
        val sort = l.sortWith((a, b) => a > b)
        println("sorted list " + sort)
    }
}
</pre>
scala支持curry函数，也就是有多组参数的函数。下面这段程序示例curry函数的使用。
<pre class="brush:scala">
object Curry {
    def main(args: Array[String]): Unit = {
        // sum是一个curry函数，接受两组参数
        val v = sum(3)(4)
        println(v)

        // 把sum的第一组参数特化
        // _表示后面的其余所有参数
        val s1 = sum(1) _
        val v1 = s1(3)
        println(v1)

        // 把sum的第一组参数特化
        // _表示后面的其余所有参数（有两组）
        val s2 = sum3(1) _
        val v2 = s2(2)(3)
        println(v2)

        // 把sum的前两组参数特化
        // _表示后面的其余所有参数
        val s3 = sum3(1)(2) _
        val v3 = s3(3)
        println(v3)

        // 对特化后的函数再特化
        // 这个地方不需要_了 ???
        val s4 = s2(2)
        val v4 = s4(3)
        println(v4)

        // sum2是一个curry函数
        // 它有两组参数，第二组参数有两个参数
        val v5 = sum2(1)(2, 3)

        // 对sum2做特化
        val s6 = sum2(1) _
        val v6 = s6(2, 3)
    }

    def sum(x: Int)(y: Int) = x + y

    def sum3(x: Int)(y: Int)(z: Int) = x + y + z

    def sum2(x: Int)(y: Int, z: Int) = x + y + z
}
</pre>
scala支持by-name参数，这相当于C/C++的宏替换，当然比宏有更多的优点咯。在Java里没有这样的应用。
<pre class="brush:scala">
object CallByName {
    def main(args: Array[String]): Unit = {
        val o1 = new O()
        val o2 = new O()
        byName(o1 > o2)
        byValue(o1 > o2)
    }

    class O {
        def >(o: O): Boolean = {
            println("compare")
            true
        }
    }

    // by-name参数，predicate在函数内部才展开
    def byName(predicate: => Boolean) = {
        println("before predicate")
        predicate
        println("after predicate")
    }

    // by-value参数，在调用函数前就计算好了值
    def byValue(predicate: Boolean) = {
        println("before predicate")
        predicate
        println("after predicate")
    }
}
</pre>
