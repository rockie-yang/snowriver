---
ID: 359
post_title: >
  Programming in Scala 学习笔记ch2
  First step in Scala
author: riv
post_date: 2011-03-13 17:03:50
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/13/programming-in-scala-ch2/
published: true
---
混迹软件行业十年。对C、C＋＋、JAVA还算熟悉。吹下牛说精通也可以:-)。但这几种语言都已经过了鼎盛期，很难有什么太大的发展了。SUN被Oracle收购之后，JAVA语言的未来也比较渺茫。当然用JAVA语言没有任何享受的感觉。还是得学点新东西，要不然就out了。

在技术时尚人士Honnix的鼓动下，我也开始学习Scala。也就是看Programming in Scala。顺便做一下笔记，将自己试的程序拷贝下来。这些记录以Java作为主要比较对象。第一章的东西比较抽象与笼统，对我来说还是得来点实际的。直接看第二章，写了两个程序。

第一个Hello World。这个例子就是一些主要的语法区别。从这个Hello World中可以看到语言设计人性的地方。不用写很多重复的东西。 
<pre class="brush:scala">
// 相当于singleton
object HelloWorld {
    // object里的函数相当于静态函数
    // args 参数名
    // Array[String] 参数类型
    // Unit 返回类型，相当于java的void
    def main(args: Array[String]): Unit = {
        // 定义字符串常量，相当于java的
        // final String exp = "Hello World";
        val exp: String = "Hello World"

        // 编译器可以侦测到的类型不需要显式定义类型.
        // 所以也可以写成下面的方式
        // scala中有很多都可以省略，新做的编译器智能化多了.
        // 最后的分号也是可以省略的。
        // 不过Scala的Eclipse的插件如果没有；就会缩进。
        val imp = "Hello World"

        // 定义变量，相当于java的
        // String v = "Hello"
        var v = "Hello"
        v += " World"

        // 打印。相当于java的
        // System.out.println(v);
        println(v)
    }
}</pre>

第二个程序是关于循环的。这和Java已经有比较大的区别了，用到了函数式编程。因为以前看过Python，所以还比较好懂。
<pre class="brush:scala">
object Loop {
    def main(args: Array[String]): Unit = {
        // 和JAVA一样的循环
        var i = 0
        while (i < args.length) {
            println(args(i))
        }
        
        for (arg <- args)
        	println(arg)
        
        // 用函数式编程的for each
        // 对数组args作遍历
        // 每个遍历的值放到arg里
        // 对每次遍历调用println(arg)
        args.foreach(arg => println(arg))

        
        // 更加简化的可以用
        args.foreach(println)
    }
}
</pre>