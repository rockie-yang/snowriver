---
ID: 465
post_title: >
  Programming in Scala 学习笔记ch10
  composition and inheritance
author: riv
post_date: 2011-03-24 06:38:32
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/24/programming-in-scala-ch10-composition-and-inheritance/
published: true
---
scala有不少人性化的设计，能让程序员用起来很爽。

工厂对象。类和工厂对象名字一样，在工厂对象中定义生成类对象的工厂方法。再通过重载函数的方法使客户程序无需关注类层次关系的实现细节。

属性方法和属性成员使用完全一致。这使得客户程序无需关心到底是用函数实现的还是变量实现的。在继承时也可以通过覆盖改变实现方法。库实现者可以根据需要随时改变。需要性能时改成变量，需要灵活性时改用函数。

构造函数中定义的参数可以当成员变量直接使用。不需要多此一举再重新定义一遍成员变量并赋值。

下面是一个小例子来示例，组合，继承，工厂对象，工厂方法，属性方法。<!--more-->
<pre class="brush:scala">
import Element.elem

// 定义一个抽象类
abstract class Element {
    // 只有声明没有，没有定义
    def contents: Array[String]

    // height是一个函数
    // scala里没有参数的函数可以成员变量一模一样使用
    // 定义从成员变量换到成员函数时客户代码就不用任何改变
    // 反之亦然
    def height: Int = contents.length

    def width: Int = if (height == 0) 0 
                             else contents(0).length

    // 这个函数从语法上来说没任何特别的
    // 从语义上来说就是在本对象放在一个其它对象的上方
    def above(that: Element): Element = {
        // 要放在上方，那么宽度的一样
        // 所以先把它们拓宽
        // 下面两条语句最多只会有一句执行
        val this1 = this widen that.width
        val that1 = that widen this.width

        // 然后再将它们连在在一起
        elem(this1.contents ++ that1.contents)
    }

    def beside(that: Element): Element = {
        // 要放在边上，那么高度的一样
        // 所以先把它们拉高成高的那个
        // 下面两条语句最多只会有一句执行
        var this1 = this heighten that.height
        var that1 = that heighten this.height

        // List(1, 2, 3) zip List(4, 5) == List((1,4), (2,5))
        // 个数不一样的会被去掉。
        // 由于上面已经将它拉成一样高了
        // 所以这儿不会有元素会被去掉
        elem(
            for ((line1, line2) <- this1.contents 
                zip that1.contents)
            yield line1 + line2)
    }

    // 将本对象拓宽如果不够宽
    def widen(w: Int): Element = {
        // 如果已经够宽了
        if (w <= width) this
        else {
            // 左边加上不够宽一半的空格
            val left = elem(' ', (w - width) / 2, height)
            // 其余不够宽的空格加到右边
            var right = elem(' ', w - width - left.width, 
                height)
            // 将左右两边的空格加上去
            left beside this beside right
        }
    }

    // 将本对象加高如果不够高
    def heighten(h: Int): Element = {
        // 如果已经够高了
        if (h <= height) this
        else {
            // 上面加上不够高一半的行数
            val top = elem(' ', width, (h - height) / 2)
            // 其它不够高的加到下面
            var bot = elem(' ', width, 
                h - height - top.height)
            top above this above bot
        }
    }

    override def toString = contents mkString "\n"
}

// 对象Element和类Element一起出现。这里作为工厂对象
object Element {
    def main(args: Array[String]): Unit = {
        val e = elem("Hello") above elem("World")
        println(e)

        val ae = elem(Array("Hello", "World"))

        val le = elem("Hello World Line")

        val ue = elem('*', 5, 2)

        println(ae above (le beside ue))
    }

    // 工厂方法
    def elem(contents: Array[String]): Element =
        new ArrayElement(contents)

    def elem(char: Char, width: Int, height: Int) =
        new UniformElement(char, width, height)

    def elem(line: String): Element =
        new LineElement(line)
}

// contents会把基类的声明加上定义
class ArrayElement(val contents: Array[String]) 
    extends Element

// 调用基类的构造函数
class LineElement(s: String) extends
ArrayElement(Array(s)) {
    // 覆盖函数定义
    override def width = s.length

    override def height = 1
}

class UniformElement(ch: Char,
                     // 用成员变量覆盖成员函数
                     override val width: Int,
                     override val height: Int)
    extends Element {

    // 私有成员变量
    private val line = ch.toString * width

    def contents = Array.fill(height)(line)
}
</pre>
scala程序比较喜欢使用递归，而这有时候就没有那么好理解。像上面的程序中就使用了。beside调用heighten，heighten又调用above，above又调用widen，widen又调用beside。这些递归调用在一下库的实现中还无所谓，如果是平常产品使用，应该尽量少用。否则维护的人会比较抓狂，当然全部是牛人例外。
