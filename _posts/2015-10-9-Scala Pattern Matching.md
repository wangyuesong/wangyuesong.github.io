---
layout: post
category: Scala
title: Scala Pattern Matching
tagline: by Archer
tags:
- Scala

---
原文 https://www.artima.com/pins1ed/case-classes-and-pattern-matching.html

Scala的语法及其繁复，本文根据英文的资料，总结了一些关于Scala中Case Class及其背后的复杂的Pattern Matching原理

###	Case class

考虑如下的一段代码，假设我们在设计一种数学运算的程序语言，如下是数学表达式，包括变量，数字，一元运算符以及二元运算符
{% highlight scala %}
    abstract class Expr
    case class Var(name: String) extends Expr
    case class Number(num: Double) extends Expr
    case class UnOp(operator: String, arg: Expr) extends Expr
    case class BinOp(operator: String, 
        left: Expr, right: Expr) extends Expr

{% endhighlight %}
Case 关键词加在了每一个class前面，进而：

#### 1.为每一个class都添加了一个工厂方法，用来产生该类的实例

{% highlight scala %}
  scala> val v = Var("x")
  v: Var = Var(x)
{% endhighlight %}

否则就需要使用new关键字
{% highlight scala %}
	val v = new Var("x")
{% endhighlight %}
要注意scala中类的constructor并不像java一样表现为一个成员方法，而是表现为跟随在类名后的括号里
{% highlight scala %}
  class A(x:Int)
  {
  val b:Int = x;
  }
{% endhighlight %}
case class会自动将括号中的parameter维护为同名的成员变量

#### 2.自动生成toString, hashCode 以及equals方法

比如print方法会智能的打印出整个类实例的结构以及成员变量当前值
{% highlight scala %}
  scala> val op = BinOp("+", Number(1), v)
  op: BinOp = BinOp(+,Number(1.0),Var(x))
  scala> println(op)
  BinOp(+,Number(1.0),Var(x))
{% endhighlight %}

### Pattern Matching

现在对于这些运算你想产生一些简化的规则，比如负负得正（一元运算符），+0（二元运算符）直接返回原来值等等
{% highlight scala %}
  UnOp("-", UnOp("-", e))  => e   // Double negation
  BinOp("+", e, Number(0)) => e   // Adding zero
  BinOp("*", e, Number(1)) => e   // Multiplying by one
{% endhighlight %}

可以写一个这样的方法，参数为一个表达式，返回一个简化后的表达式
{% highlight scala %}
  def simplifyTop(expr: Expr): Expr = expr match {
      case UnOp("-", UnOp("-", e))  => e   // Double negation
      case BinOp("+", e, Number(0)) => e   // Adding zero
      case BinOp("*", e, Number(1)) => e   // Multiplying by one
      case _ => expr
    }
{% endhighlight %}

这其中"+"被视作constant pattern,如果x想和它匹配，需要x=="+"返回true

e 被视作variable pattern,它匹配任意的东西，并且可以在=>右侧被引用到

_ 被视作wildcard pattern,它同样匹配任意的东西，只是不能在右侧被引用

UnOp("-",e) 被视作constructor pattern,它首先会去检查被匹配的表达式是否是UnOp的type的，是的话，继续匹配UnOp的两个constructor field,第一个field需要
进行constant pattern匹配，第二个e匹配任何东西，属于一种vairable pattern。

因为e能够匹配任何东西，所以也可以嵌套匹配，比如：
{% highlight scala %}
  UnOp("-", UnOp("-", e))
{% endhighlight %}

##总结一下各类Patterns:

###	1.Wildcard Pattern _

总结来说，这个pattern不会干扰某一个case匹配是否成功，因为它可以匹配任何东西
{% highlight scala %}
  expr match {
    case BinOp(_, _, _) => println(expr +"is a binary operation")
    case _ => println("It's something else")
  }
{% endhighlight %}
这里面第一个case的意思是，只要是Expression是BinaryOp类型的就可以

###	2.Constant Pattern 
Constant Pattern只和itself匹配

###	3.Variable Pattern
Variable Pattern也和任意Object匹配，只不过右侧可以引用到

这里有一个很重要的点，Variable Pattern使用的 variable要是小写开头的，使用大写，Scala会把它当做constant pattern
{% highlight scala %}
  scala> val pi = Math.Pi
  pi: Double = 3.141592653589793
  
  scala> E match {
           case pi => "strange math? Pi = "+ pi
        }
res11: java.lang.String = strange math? Pi = 2.7182818...
        
{% endhighlight %}
在这里，pi就被认定为是一个variable pattern，编译器甚至不允许你再加_匹配，因为variable pattern会匹配所有东西

如果想把小写的variable进行constant匹配，可以这样 `pi`，这样编译器就会去寻找名称为pi的val，进行constant匹配，而不是把它当做variable pattern

{% highlight scala %}
scala> E match {
           case `pi` => "strange math? Pi = "+ pi
           case _ => "OK"
         }
  res13: java.lang.String = OK
{% endhighlight %}

###	4.Constructor Pattern
Constructor 是一种支持嵌套的匹配类型，可以进行深度匹配
{% highlight scala %}
expr match {
      case BinOp("+", e, Number(0)) => println("a deep match")
      case _ =>
    }

{% endhighlight %}

这个匹配中就进行了三层匹配
第一层：首先被匹配的case class的Object需要是BinOp类型的
第二层：BinaryOp中，第一个parameter进行constant匹配，需要是+ 。 第二个paramter无所谓，第三个paramter需要时Number类型的
第三层：NUmber中，第一个paramter进行constant匹配，是0

###	5.Sequence Pattern
可以向对case class的匹配一样，对List，Array这样的sequence type进行匹配：
{% highlight scala %}
    expr match {
      case List(0, _, _) => println("found it") //三个元素的list，第一个是0
      case _ =>
    }
   //Or
   
      expr match {
      case List(0, _*) => println("found it") //变长List,第一个是0
      case _ =>
    }
{% endhighlight %}
稍微分析一下，其实和Constructor Pattern很像，
第一层：首先被匹配的expr 是不是List类型的
第二层: 0 进行 constant 匹配， _  进行wildcard匹配

###	6.Tuple Pattern

同样，可以对元组进行匹配
{% highlight scala %}
    def tupleDemo(expr: Any) =
      expr match {
        case (a, b, c)  =>  println("matched "+ a + b + c)
        case _ =>
      }
{% endhighlight %}

注，(a,b,c)会产生一个元组类型Tuple3的实例
  
###	7.Type Pattern
Type Pattern进行类型匹配和类型向下转换
{% highlight scala %}
def generalSize(x: Any) = x match {
      case s: String => s.length
      case m: Map[_, _] => m.size
      case _ => -1
    }
{% endhighlight %}
注意，x的类型是Any，s的类型是String，尽管如果匹配到了case s，两者其实指向的是同一个东西，但是x作为Any，没有.length方法
如果不使用pattern matching，在scala中是这样进行类型匹配和向下转换的

{% highlight scala %}
    if (x.isInstanceOf[String]) {
      val s = x.asInstanceOf[String]
      s.length
    } else ...
{% endhighlight %}
x.asInstanceOf[String]其实是调用了any中的一个泛型方法asInstanceOf[T]()


###	8.Type erasure

之前所谓的对List的匹配
{% highlight scala %}
List(0, _*)
{% endhighlight %}
其实在第一层匹配是List之后，第二层的匹配都只是constant，wildcard或者constructor的了
这一点太难了，不懂

### 9.Variable binding
不仅仅是variable pattern可以在右侧引用，比如这里的Constructor pattern在使用@起名后，也可以在右侧使用
{% highlight scala %}
expr match {
      case UnOp("abs", e @ UnOp("abs", _)) => e
      case _ =>
    }
{% endhighlight %}
