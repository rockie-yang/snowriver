---
ID: 423
post_title: >
  Programming in Scala 学习笔记ch8
  function and closer
author: riv
post_date: 2011-03-20 18:25:44
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/20/programming-in-scala-ch8-function-and-closer/
published: true
---
函数里面可以嵌套函数。嵌套函数可以直接用外部函数的参数，以及在该函数之前的变量。
<pre class="brush:scala">
    def processFile(filename: String, width: Int) {
         val m = width * 3
        def processLine(line: String) {
            // width这里可以直接使用，m也可以直接使用
            if (line.length > width && line.length < m)
                println(filename + ": " + line)
        }

        val source = Source.fromFile(filename)
        for (line <- source.getLines())
            processLine(line)
    }
</pre>

scala里函数是一个对象。函数不仅可以用传统的方法静态定义，也可以这执行是动态定义。<!--more-->
<pre class="brush:scala">
object Function {
    def main(args: Array[String]): Unit = {
        var increase = (x: Int) => x + 1
        println(increase(10))

        increase = (x: Int) => x + 5
        println(increase(10))

        val l = List(1, 3, 5, 7, 9, 11)
        l.foreach(increase)
        println(l)

        val l2 = l.filter((x: Int) => x > 2)
        println(l2)

        // x的类型可以根据List l的内容推导出来
        // 所以x的类型定义可以省略掉
        val l3 = l.filter(x => x > 3)
        println(l3)

        // 既然可以写成前面那样的格式
        // 那么x也是多余的了
        // 可以用_占位符直接匹配
        val l4 = l.filter(_ > 5)
        println(l4)

        // 不光可以匹配一个参数，还可以匹配多个
        // 如果参数不可推导，那么需要把类型显式定义出来
        val f = (_: Int) + (_: Double)
        val v5 = f(3, 5)
        println(v5)

        // _还可以匹配一个参数列表
        val asum = sum _
        val v6 = asum(1, 2, 3)
        println(v6)

        // 可以将其它参数的值指定，赋给另一个变量
        val psum = sum(3, _ : Int, 5)
        val v7 = psum(7)
        println(v7)
    }

    def sum(a: Int, b: Int, c: Int): Int = {
        a + b + c
    }
}
</pre>
有个概念叫做闭包。这个名称实在是不怎么可以自解释。下面代码示例闭包定义，以及一个简单用法。
<pre class="brush:scala">
object Closer {
    def main(args: Array[String]): Unit = {
        // 定义一个函数的时候，里面的某个值可以是在外面定义的
        // 这种函数叫闭包（closer）
        // 这个值叫做自由变量
        var more = 0
        val mf = (_: Int) + more
        println(mf(10))

        // 自由变量可以改变值
        more = 10
        println(mf(10))

        // 这儿就可以看到这种用法的实用场景了
        val l = List(1, 3, 4, 5, 9)
        var sum = 0
        l.foreach(sum += _)
        println(sum)
    }
}
</pre>

scala函数也支持可变参数。使用也比较方便。
<pre class="brush:scala">
object RepeatedParams
{
    def main(args : Array[String]) : Unit = {
        echo()
        echo("Hello")
        echo("Hello", "World")

        val a = Array("Hello", "world")
        // 这种格式告诉编译器将a里面的每一个元素当作参数传给echo
        echo(a : _*)
    }

    // 定义可变参数
    // 这种定义args实际上是一个WrappedArray
    def echo(args : String*) : Unit = {
        for (arg <- args)
            println(arg)
    }
}
</pre>
scala支持命名参数。这种用法这有些情况下使用非常方便。
<pre class="brush:scala">
object NamedArgument {
    def main(args: Array[String]): Unit = {
        val a = speed(distance = 100, time = 9)
        println(a)

        val b = speed(time = 10, distance = 101)
        println(b)
    }

    def speed(distance: Double, time: Double) = distance / time
}
</pre>