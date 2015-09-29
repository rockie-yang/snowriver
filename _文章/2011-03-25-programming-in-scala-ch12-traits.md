---
ID: 487
post_title: >
  Programming in Scala 学习笔记ch12
  traits
author: riv
post_date: 2011-03-25 17:57:49
post_excerpt: ""
layout: post
permalink: >
  http://snowriver.org/blog/2011/03/25/programming-in-scala-ch12-traits/
published: true
---
很多面向对象语言，都只能从一个类继承，因为多重继承会有众所周知乱辈份问题。而这限制了多个特性的应用。而一个对象可以有多个特性。scala里通过特性(traits)混入(mixin)方式来解决这个问题。下面是一个基本的例子。
<pre class="brush:scala">
class Point(val x: Int, val y: Int)

trait Rectangular {
    // 声明左上角和右下角的点
    // 这两个作为其它成员的基础
    def topLeft: Point
    def bottomRight: Point

    def left = topLeft.x

    def right = bottomRight.x

    def width = right - left
    // and many more geometric methods...
}

class Rectangle(val topLeft: Point,
                val bottomRight: Point)
    extends Rectangular
</pre>
如果仅仅是上面这个例子，通过类继承也完全可以实现。但是如果要加一个Display特性呢？那也只能添加到基类当中，或者是定义Display接口。如果定义Display接口，那么派生类就必须实现它，而且只能这个派生类的体系当中使用。如果这个Display特性还想在其它非相关的类当中使用呢？trait就很好的解决了这个问题。<!--more-->

那么上面的scala到底会编译成什么样呢? 想知道编译器到底变了什么魔术，最好的办法莫过于将生成的代码反编译一把。JD是一个不错的工具，JD可以从网站http://java.decompiler.free.fr/下载。上面的scala代码会编译成下面这样的Java代码（去掉了一些无关紧要的代码）。
<pre class="brush:java">
interface Rectangular {
	Point topLeft();
	Point bottomRight();
	int left();
	int right();
	int width();
}
public abstract class Rectangular$class {
	public static int left(Rectangular $this) {
		return $this.topLeft().x();
	}

	public static int right(Rectangular $this) {
		return $this.bottomRight().x();
	}

	public static int width(Rectangular $this) {
		return $this.right() - $this.left();
	}
}
public class Rectangle implements Rectangular {
	private final Point topLeft;
	private final Point bottomRight;

	public int left() {
		return Rectangular$class.left(this);
	}

	public int right() {
		return Rectangular$class.right(this);
	}

	public int width() {
		return Rectangular$class.width(this);
	}

	public Point topLeft() {
		return this.topLeft;
	}

	public Point bottomRight() {
		return this.bottomRight;
	}

	public Rectangle(Point topLeft, Point bottomRight) {
		this.topLeft = topLeft;
		this.bottomRight = bottomRight;
	}
}
</pre>
从上面的代码我们可以看到一个trait Rectangular会生成一个接口Rectangular，一个类Rectangular$class（ $符在Java里可以直接使用，这些代码可以用Java直接编译）。Rectangular$class所有的函数都是静态函数，并且第一个参数都是名字叫$this的Rectangular。在混入trait的类Rectangle会实现Rectangular接口。每个函数都是调用Rectangular$class的静态函数，并把自己作为$this参数传入。

trait还支持叠加功能(Stackable)，也就是trait里的函数会一个一个执行，就像压到堆栈里一样。下面这个scala例子里，有两个trait，Incrementing和Filtering。如果想让一个类先做Filtering再做Incrementing，只要让这类混入这两个trait就可以了。放再最后的trait会先应用，让后往左一个一个应用其它trait。
<pre class="brush:java">
import scala.collection.mutable.ArrayBuffer

class Point(val x: Int, val y: Int)

abstract class IntQueue {
    def get(): Int
    def put(x: Int)
}
class BasicIntQueue extends IntQueue {
    private val buf = new ArrayBuffer[Int]
    def get() = buf.remove(0)
    def put(x: Int) { buf += x }
}

trait Incrementing extends IntQueue {
    abstract override def put(x: Int) { super.put(x + 1) }
}
trait Filtering extends IntQueue {
    abstract override def put(x: Int) {
        if (x >= 0) super.put(x)
    }
}

class IncFiltQueue extends BasicIntQueue
    with Incrementing with Filtering
</pre>
想要探究一下编译器背后的魔法，那么还是用反编译这一招。trait相关的代码会生成下面的Java代码（去掉了无关部分）。
<pre class="brush:java">
public interface Incrementing {
	void Incrementing$$super$put(int paramInt);

	void put(int paramInt);
}
public abstract class Incrementing$class {
	public static void put(Incrementing $this, int x) {
		$this.Incrementing$$super$put(x + 1);
	}
}
public interface Filtering {
	void Filtering$$super$put(int paramInt);

	abstract void put(int paramInt);
}
public abstract class Filtering$class {
	public static void put(Filtering $this, int x) {
		if (x >= 0)
			$this.Filtering$$super$put(x);
	}
}
public class IncFiltQueue 
    extends BasicIntQueue 
    implements Incrementing, Filtering {
	public final void Filtering$$super$put(int x) {
		Incrementing$class.put(this, x);
	}

	public void put(int x) {
		Filtering$class.put(this, x);
	}

	public final void Incrementing$$super$put(int x) {
		super.put(x);
	}
}
</pre>
可以看出trait本身生成的接口以及实现类没有变化，是不是应用叠加没有任何影响。当然也应该这样，要不然怎么生成一致的代码呢！而混入trait的类就会有一些不同，会将trait里的那些函数一一应用上去。

从上面的说明来看trait好像就是一个银弹。那么再什么情况下应该使用类继承，什么情况下用trait呢？有一些规则可以作为参考。
<ul><li>如果行为没有重用的可能，那么就可以使用类继承</li>
<li>如果行为会被重用，特别是会被不相关的多个类里重用，那么就可以使用trait</li>
<li>如果想被Java继承，那么就不能使用trait</li>
<li>如果库会以二进制形式发布，并且会被外部继承，那么就不要使用trait</li>
<li>如果性能至关重要，那么可以考虑使用类继承，trait会有一点点额外开销</li>
</ul>