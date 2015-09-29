---
ID: 518
post_title: >
  Programming in Scala 学习笔记ch14
  assertions and unit testing
author: riv
post_date: 2011-03-28 22:25:57
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/28/programming-in-scala-ch14-assertions-and-unit-testing/
published: true
---
断言就是判断一下条件是否为真，如果为真就什么也不做，否则就抛出一个AssertionError异常。assert有两种格式。一个是只接受一个条件。另一个是接受一个条件和一个提示，当条件为假时会把提示信息打印出来。
<pre class="brush:scala">
assert(1 > 2)
assert(1 > 2, "1 is impossible to larger then 2")
</pre>
scala还支持另外一种断言ensuring，在函数返回之前检查一下返回值是否是期望的。如果不是希望的，行为和assert一样。实际上ensuring是assert的简单包装。下面这段程序示例ensuring的使用。
<pre class="brush:scala">
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
        } ensuring (w <= _.width)
    }
</pre>
上面ensuring的语法是又点搞的。可以试试将ensuring放到下一行，那么编译是通不过的。ensuring实际上是应用到else子句的值上。else子句的值是Element。<!--more-->
<pre class="brush:scala">
    // 所以
        left beside this beside right
    } ensuring (w <= _.width)
    // 实际上就相当于
        val element = left beside this beside right
    } element ensuring (w <= _.width)
    // element 对象会通过隐式转换转换为一个Ensuring对象
    implicit def any2Ensuring[A](x: A): Ensuring[A] 
        = new Ensuring(x)
    // 所以上面就相当于
        new Ensuring(element) ensuring (w <= _.width)
    // _代表的是前面的element对象，所以又相当于
        new Ensuring(element) ensuring (w <= element.width)
</pre>
受益于和Java之间无缝操作。Java的测试库，像JUnit、TestNG，可以直接用于scala的单元测试。ScalaTest是专为scala设计的一个测试框架。ScalaTest提供了一些比较方便的功能。如果喜欢JUnit3的风格，那么可以写下面这样的单元测试。
<pre class="brush:scala">
class ElementSuite1 extends JUnit3Suite {
    def testUniformElement() {
        val ele = elem('x', 2, 3)
        // 用===，在结果为false的时候打印出两边的值
        // 以利于查找问题。也可以用assertEquals来代替
        assert(ele.width === 2)
        // 和上面的目的是一模一样的。只是用了另一种风格而已
        expect(3) {
            ele.height
        }
        // 如果结果为假时还能打印出相关信息
        expect(3, "the height should be that value") {
            ele.height
        }
        // 这是比JUnit使用方便的地方。
        // 期望执行结果抛出异常
        intercept[IllegalArgumentException] {
            elem('x', -2, 3)
        }
    }
}
</pre>
如果喜欢JUnit4的风格，那么可以写下面这样的单元测试。
<pre class="brush:scala">
class ElementSuite2 extends JUnitSuite{
	@Test
    def uniformElement() {
        val ele = elem('x', 2, 3)
        assert(ele.width === 2)
        expect(3) {
            ele.height
        }
        expect(3, "the height should be that value") {
            ele.height
        }
        intercept[IllegalArgumentException] {
            elem('x', -2, 3)
        }
    }
}
</pre>
ScalaTest还提供了函数式的单元测试。
<pre class="brush:scala">
// 用函数式的方式来写单元测试
// IDE目前对ScalaTest的支持不是特别好
// 加上RunWith就可以用JUnit的方式来运行了
@RunWith(classOf[JUnitRunner])
class ElementSuite3 extends FunSuite {
    test("elem result should have passed width") {
        val ele = elem('x', 2, 3)
        assert(ele.width == 2)
    }
}
</pre>
单元测试对提高软件质量很有好处。唯一的不足就是只针对程序员。其它人员要看懂还是比较有困难。ScalaTest提供了BDD（Behavior Driven Development行为驱动开发）测试方式。下面的这段测试代码在运行时就会打印出可读的解释。
<pre class="brush:scala">
class ElementSpec extends FlatSpec with ShouldMatchers {
    "A UniformElement" should
        "have a width equal to the passed value" in {
        val ele = elem('x', 2, 3)
        ele.width should be(2)
    }
    it should
        "have a height equal to the passed value" in {
        val ele = elem('x', 2, 3)
        ele.height should be(3)
    }
    it should
        "throw an IAE if passed a negative width" in {
        evaluating {
            elem('x', -2, 3)
        } should produce[IllegalArgumentException]
    }
}
</pre>
上面的代码会打印出下面这样的提示。
<pre>
A UniformElement 
- should have a width equal to the passed value
- should have a height equal to the passed value
- should throw an IAE if passed a negative width
</pre>
我们写单元测试时会测试一些边界值。然后再选一些典型的值。如果这些选值有库来做，不但可以减少单元测试的工作量，而且可以将边界值选取更合理。下面是如何将ScalaChecker和ScalaTest联合起来使用的一个例子。
<pre class="brush:scala">
class ElementSpecChecker extends FlatSpec with ShouldMatchers with Checkers{
    "A UniformElement" should
        "have a width equal to the passed value" in {
    	// 这可以用数学化的方式来读
    	// 对每个整数w
    	// 当w>0时
    	// 都有后面的等式成立
        check((w: Int) => w > 0 ==> (elem('x', w, 3).width == w))
    }
}
</pre>
