---
ID: 417
post_title: >
  Programming in Scala 学习笔记ch7
  Built-in control structure
author: riv
post_date: 2011-03-20 18:23:24
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/20/programming-in-scala-%e5%ad%a6%e4%b9%a0%e7%ac%94%e8%ae%b0ch7-built-in-control-structure/
published: true
---
<!--:en-->scala只有非常少的内建控制结构。只有if、while、for、try、match和函数调用。大部分都是通过库形式一层一层累积起来的。

和Java不同，scala里几乎每个控制结构都有返回值。也就是最后一个表达式的值。据说这样可以让编程更有效。
<pre class="brush:scala">
object TestIf  {
    def main(args: Array[String]): Unit = {
        // 通常我们基于一个条件对一个变量赋值会用下面这种方式
        // 先赋一个默认值，如果条件成立再赋一个另外的值
        var filename = "default.txt"
        if (!args.isEmpty)
            filename = args(0)

        // 在scala里由于if语句有返回值，可以直接这么写。
        // 上面那种方式只能定义为变量，而这种情况可以定义为值。
        // 读程序的人就知道这是一个不会变的值
        val filename2 =
            if (args.isEmpty) "default.txt" else args(0)
        println (filename2)

    }
}
</pre>
<!--:--><!--:zh-->scala只有非常少的内建控制结构。只有if、while、for、try、match和函数调用。大部分都是通过库形式一层一层累积起来的。

和Java不同，scala里几乎每个控制结构都有返回值。也就是最后一个表达式的值。据说这样可以让编程更有效。
<pre class="brush:scala">
object TestIf  {
    def main(args: Array[String]): Unit = {
        // 通常我们基于一个条件对一个变量赋值会用下面这种方式
        // 先赋一个默认值，如果条件成立再赋一个另外的值
        var filename = "default.txt"
        if (!args.isEmpty)
            filename = args(0)

        // 在scala里由于if语句有返回值，可以直接这么写。
        // 上面那种方式只能定义为变量，而这种情况可以定义为值。
        // 读程序的人就知道这是一个不会变的值
        val filename2 =
            if (args.isEmpty) "default.txt" else args(0)
        println (filename2)

    }
}
</pre>
<!--:--><!--more--><!--:en-->
scala里的for循环还是不错的。下面代码示例几种常用的用法。
<pre class="brush:scala">
object TestFor {
    def main(args: Array[String]): Unit = {
        val files = (new java.io.File(".")).listFiles()

        // 对里面的每个文件循环
        // file知识一个一般的值变量，写成其它的也可以
        for (file <- files) println(file)

        // 譬如下面就写成了f
        for (f <- files) println(f)

        // 1 to 5 实际上返回的是一个Range(1, 6)
        // 就是对1,2,3,4,5做循环
        for (i <- 1 to 5) println(i)

        // 0 until 5 实际上返回的是一个Range(0, 5)
        // 就是对0, 1,2,3,4做循环
        for (i <- 0 until 5) println(i)

        // for 里面还可以添加if来过滤
        for (
            file <- files if (file.getName.endsWith("scala"))
        ) println(file)

        // for里面还可以有多个过滤条件
        for (
            file <- files if (file.isFile) 
            if (file.getName.endsWith("scala"))
        ) println(file)

        // for循环里找出来的东西还可以放到一个集合里
        val sfiles = for (
            file <- files 
            if (file.isFile) 
            if (file.getName.endsWith("scala"))
        ) yield file

        println(sfiles)
    }
}
</pre>
scala里的异常处理表面上的用法和Java差不多。但是scala里不要定义哪些异常会抛出。finally里要关闭的东西还是要在try外面定义。
<pre class="brush:scala">
import java.io.FileReader
object TestTry {
    def main(args : Array[String]) : Unit = {
    	var f = new FileReader("test.txt")
        try{
            // use the file
        }
        catch{
            case ex : java.io.FileNotFoundException =>
                println (ex)
            case ex : java.io.IOException =>
                println (ex)
        }
        finally {
            f.close()
        }
    }
}
</pre>

scala支持使用match来支持多分支。和Java里的switch不同，scala的match不关能在整数上使用，还可以在字符串等类型上使用。
<pre class="brush:scala">
val firstArg = if (!args.isEmpty) args(0) else ""
val friend = firstArg match {
    // 每个case后不需要用break
    // case可以有值
    case "salt" => "pepper" 
    case "chips" => "salsa" 
    case "eggs" => "bacon" 
    // _和default的作用一样
    case _ => "huh?"
}
</pre><!--:--><!--:zh-->
scala里的for循环还是不错的。下面代码示例几种常用的用法。
<pre class="brush:scala">
object TestFor {
    def main(args: Array[String]): Unit = {
        val files = (new java.io.File(".")).listFiles()

        // 对里面的每个文件循环
        // file知识一个一般的值变量，写成其它的也可以
        for (file <- files) println(file)

        // 譬如下面就写成了f
        for (f <- files) println(f)

        // 1 to 5 实际上返回的是一个Range(1, 6)
        // 就是对1,2,3,4,5做循环
        for (i <- 1 to 5) println(i)

        // 0 until 5 实际上返回的是一个Range(0, 5)
        // 就是对0, 1,2,3,4做循环
        for (i <- 0 until 5) println(i)

        // for 里面还可以添加if来过滤
        for (
            file <- files if (file.getName.endsWith("scala"))
        ) println(file)

        // for里面还可以有多个过滤条件
        for (
            file <- files if (file.isFile) 
            if (file.getName.endsWith("scala"))
        ) println(file)

        // for循环里找出来的东西还可以放到一个集合里
        val sfiles = for (
            file <- files 
            if (file.isFile) 
            if (file.getName.endsWith("scala"))
        ) yield file

        println(sfiles)
    }
}
</pre>
scala里的异常处理表面上的用法和Java差不多。但是scala里不要定义哪些异常会抛出。finally里要关闭的东西还是要在try外面定义。
<pre class="brush:scala">
import java.io.FileReader
object TestTry {
    def main(args : Array[String]) : Unit = {
    	var f = new FileReader("test.txt")
        try{
            // use the file
        }
        catch{
            case ex : java.io.FileNotFoundException =>
                println (ex)
            case ex : java.io.IOException =>
                println (ex)
        }
        finally {
            f.close()
        }
    }
}
</pre>

scala支持使用match来支持多分支。和Java里的switch不同，scala的match不光能在整数上使用，还可以在字符串等类型上使用。
<pre class="brush:scala">
val firstArg = if (!args.isEmpty) args(0) else ""
val friend = firstArg match {
    // 每个case后不需要用break
    // case可以有值
    case "salt" => "pepper" 
    case "chips" => "salsa" 
    case "eggs" => "bacon" 
    // _和default的作用一样
    case _ => "huh?"
}
</pre><!--:-->