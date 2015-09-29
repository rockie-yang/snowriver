---
ID: 411
post_title: >
  Programming in Scala 学习笔记ch6
  functional objects
author: riv
post_date: 2011-03-19 18:37:29
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/19/programming-in-scala-ch6-functional-objects/
published: true
---
下面例子定义一个比前面定义稍微复杂一点的类Rational（分数）。
<pre class="brush:scala">
class Rational(n: Int, d: Int) {
    // 检查输入参数
    require(d != 0, "d can not be 0")

    // 定义私有成员
    private val g = gcd(n, d)

    // 定义成员变量，默认为public，外部可以访问
    val numer = n / g
    val denom = d / g

    // 定义其它构造函数
    def this(n: Int) = this (n, 1)

    override def toString = n + "/" + d

    def ＋(that: Rational): Rational = {
        new Rational(this.numer * that.denom
            + that.numer * this.denom,
            this.denom * that.denom)

        // this可以忽略
        new Rational(numer * that.denom + that.numer * denom,
            denom * that.denom)

        // numer可以使用n代替，denom可以使用d代替
        // 但是that.denom不能用that.d代替，
        // that.number不能用that.n代替
        // 不知道scala为什么设计成这样
        new Rational(n * that.denom + that.numer * d,
            d * that.denom)
    }

    def <(that: Rational): Boolean = {
        numer * that.denom < denom * that.numer
    }

    def max(that: Rational): Rational = {
        if (this < that) that else this
    }

    // greatest common divisor
    // 最大因子
    private def gcd(a: Int, b: Int): Int = {
        if (b == 0) a else gcd(b, a % b)
    }
}
</pre><!--more-->

scala的标志符稍微有点复杂。有几点值得记录下来。<ul>
<li>$是一个有效的标志符，但是在程序里一般不用。编译器会将操作符翻译为以$开头的一些标志符。</li>
<li>_也是一个有效的标志符，但是程序定义最好也不用。_在scala里不光用作标志符</li>
<li>所有可打印的ASCII字符都可以作为操作符。</li>
<li>编译器会把这些操作符转为合法的Java标志符。如:->会解释为$colon$minus$greater。</li>
<li>如果在Java中要访问这些操作符那就只能用像$colon$minus$greater的函数了。</li>
<li>如果为Java访问设计考虑，最好为这些操作符用一个常见的函数名包装一下</li>
<li>调用Java中的函数，而这些函数在scala中是关键字，那么需要用反撇将函数名包起来。像Thread.yield()，需要用Thread.`yield`()</li>
</ul>

在前面例子中定义了＋操作符，用于两个分数相加。如果我们想要一个整数和分数相加就需要用到隐式转换了。
<pre class="brush:scala">
val r = new Rational(1, 2)
val x = 2 + r
</pre>
上面的代码中2＋r是不能编译通过的，因为Int没用定义＋ Rational的操作符。当然返回去给Int类加上这个操作符也是可以的，但是这就改变了原来已有的东西，并且还要把Rational也加到标准库中去，那标准库就只有整天改了:-(。还有办法就是让Int能够隐式转换为Rational。需要做的是在Rational类定义中添加一个显式转换函数。函数名可以随意取，没有特别意义，它只再需要显式import的时候使用。
<pre class="brush:scala">
    implicit def intToRational(n : Int) : Rational = new Rational(n)
</pre>