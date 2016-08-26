---
layout: post
category: Coursera
title: Programming languages
tagline: by Archer
tags: [Programming Languages]
---
#### Variable binding:
Syntax只是how you write something
val a = b + 4;
一个val，一个变量名，一个等号，右边一个expression
并不检查b是不是有定义

Semantics是what something means
Type Checking (before program runs)
Evaluation(as program runs)

For each expression we have they have different syntax and type-checking rules

### Rules For Expression:
34 true x e1+e2

可以无限增大因为expression中可以含subexpression

每一个种类的expression我们都要想三个问题：
1. Syntax是什么样的
2. Type-Checking的rule是什么
3. Evaludation的rule是什么

第一种expression：Variable:
(1)Syntax: sequence of letters not starting with digit
(2)Type-Checking: 查找当前static env，没在里面就fail
(3)Evaluation: 在dynamic env中找value（不用担心是否存在因为type checking）

Addition：
(1)Syntax: e1 + e2 where e1 and e2 are expressions
(2)Type-Check: if e1 and e2 has type int, then e1 + e2 has type int
(3)Evaluation: e1 eval to v1 and e2 eval to v2, e1 + e2 eval to sum of e1 and e2

All values are expressions but not all expressions are values

Conditional Expression:
(1) Syntax if e1 then e2 else e3
where if then and else are keywords

(2)Type checking: e1 must have type bool
e2 and e3 can have any type t
the type of entire epxression is also t

(3)Evaludation rules:
first evaluate e1 to a value called v1
if true, evaluate e2 and that result is the whole result
else ...

Less than:
(1) Syntax: e1 < e2
(2) Type checking: e1 and e2 must have type int
(3) Eval: the entire expression's value will be true if value of e1 < e2

#### Shadowing
val a = 1
val b = 2
val a = 2

ML中没有reassignment

因此不要重复用use，这样会shadowing


#### Function Formally
(1)Function binding:
3 questions:
1.Syntax: fun x0(x1:t1...xn:tn) = e
2.Evaluation: A function is a value(直接自己就是一个value，和2，false这样一样，而1+1不是一个value而是一个expression，需在动态的evaluation中得到一个value)(no evaluation yet). Add x0 to environment so later expression can call it.
3.Type-checking: Adds binding x0(t1...tn)->t if can type checking body e have type t in the static environment containing:
Enclosing static env, t1...tn, x0

(2)Function calls:
1.Syntax: e0(e1...en)
2.Type checking: e0 has some type(t1...tn)->t, e1 has type t1... en has type tn
3.Evaluation：
(1)eval e0 to a function(value)
(2)evaluate argument to values(v1,v2...)
(3)Result is evaluation of e in an environment extended to map x1 to v1...xn to vn

### Tuples and lists
