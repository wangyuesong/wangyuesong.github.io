---
layout: post
category: Javascript
title: Javascript继承机制探秘
tagline: by Yuesong
tags:
- Javascript

---
本人接触javascript的时间也不长，从入职到现在使用React+Redux做了一些前端的东西，做的东西其实挺无聊的，但是对javascript这个语言有了一些更深入的了解。现在这个年代语言的基本特性都被语法糖掩盖的不成样子了，写React的时候简直就是感觉在写另外一门语言。我开始真正做前端是在ES6出现之后了，当我看到Class这个关键字时，心里一惊，不成难道Javascript真的要成Java了。
仔细查阅资料之后才发现，这只不过又是一个巨大的语法糖而已。本篇文章中我主要介绍一下我学习Javascript Class相关的历程，包含一些基本的语法，
## 1.ES6 new 语法
```javascript
class BaseClass {
	constructor(){
	}
	baseMethod(){
	}
}
class SampleClass extends BaseClass{
	constructor(){
		super();
		...
	}
	method1() {
	}
	method2() {
	}
}

let sample = new SampleClass();
sample.method1();
sample.method2(); 
sample.baseMethod();
```
上面就是ES6 class的一些基本语法，看起来和java很像。
可能有些人会问，java中与继承很类似的接口的概念去哪里了？
这其实是涉及到了一些动态/静态语言的问题，接口的概念一般不存在于动态语言中，javascript和ruby一样都是采用duck-typing，即不做编译时的类型检查，只有runtime尝试调用某个method/获取某个field时，不存在才会报错（javascript获取某个field不存在的话甚至不报错，只会返回undeifnied）

有点跑题了。


## 2.原型链继承
```java
class A{
	public void a(){}
}
class B extends A{
	public void b(){};
}

A ins = new B();
ins.a();
```

如上的Java代码中我们知道调用ins.a()时，runtime会

1. 	查找ins所指向的对象(instance)，发现对象的类型(class)是B
2. 在内存中找到class B的方法表，查找是否存在a这个函数
3. 	没有，找到B的父亲A，查找是否存在a这个函数，存在，调用之

实际的过程和如上的描述有一些出入，不过我们大致可以理解成这个意思

大部分class based的inheritance hierachy都是这样实现的，然而javascript的语言中并不存在类的概念，也就是说，一个instance被创建了，并不能找到任何一个可以称之为其“模版”的meta datastructure。每一个instance都是新建出的一个Object，里面可以存key和value，每一个instance都是独一无二，没有模版可循的。

我们知道，继承的作用主要也就是用来share code，之前的Java code中，B如果想要让自己有a这个方法，通过继承就避免了copy paste的麻烦，其实这继承就相当于是一种委托，当B自己没有这个方法时，就会委托其父类A去查看／调用这个方法。

那么Javascript（ES6之前）是怎么share code的呢？很简单，也是利用同样的一套委托机制，原理就是我没有这个方法就去我的 父类==原型object 中去找。其核心就在于object的`__proto__`这个field

```javascript
let ins1 = {
	a: function() {
	}
};

let ins2 = {
	b: function() {
	},
	__proto__: ins1
}

ins2.a();
```
上面的代码基本上和Java代码实现了同样的功能，`__proto__`这个field就相当于java中的extends，其功能是，ins2指向的object如果找不到某一个field的话，就去ins1指向的obj中去找。
但是这就带来一个问题，如果我们想要一个ins3和ins2一样，该怎么办呢

```javascript
let ins3 = {
	b: function() {
	},
	__proto__: ins1
}
```
这样写起来未免太冗长了，我们需要一个类似于模版的object，能够让很多创建出的object通过简单的语法，直接将自己找不到的field，call不出的function委托给这个模版object。
（在javascript中我们不是很区分field和function，因为function也是data，所以在后面我就只说share code了）

我作为一个obj，如果想要和别的object去share code，，那么这个code一定要存在于我的key-value pair中的value中。

很简单的想法是，好，只要你选择了我作为你的原型，那么我的code就都是你的

如上的设计就会引出一个问题

**如果我不想share 全部的code怎么办？**

那么，我把我想要share的code全部放在我的某一个固定的key(这个key，就是prototype)中去好了，key对应的value是一个object，我会把我想share的code全都按照key-value的形式塞到我的prototype中去。

语法如下：

```javascript
function Func(){
	this.a = 123;
	this.b = 234;
}
Func.prototype.sharebleFunction = () => { ... }
Func.prototype.shareableField = '123'

let obj = new Func();
```

这里由于Func是一个函数，其prototype在声明的时候就被初始化过了

这里就要引出一个新的语法：函数的new调用

其实际做的事情就是

1. 创建一个空的Object: let obj = {}
2. 让obj的__proto__指向Func.prototype: obj.__proto__ = Func.prototype
3. 在将函数的context(this)绑定在obj后，执行Func函数（constructor)
4. Func函数最后返回的Object作为整个new Expression的结果，如果不返回任何东西，就返回obj

有趣的是，其实在声明Func时：

``` javascript
function Func() {
	...
}
```

我们也是在调用new语法，相当于

``` javascript
var Func = new Function()
```

这样Func.__proto__ == Function.protype, Func作为一个原型链上端是Function的Obj（或者说Function Object），就可以使用Function.prototype中的一些操作，比如bind, apply等

附图一张

![Prototype Chain](/images/prototype_chain.png)



## 2.Everything is an Object in Javascript
## 3.How to share code
In Previous

Using new operator

## 4.new class vs new function

