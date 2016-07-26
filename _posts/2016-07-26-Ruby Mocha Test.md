---
layout: post
category: Ruby
title: Ruby Mocha Test
tagline: by Archer
tags:
- Ruby

---

Mock test的关键在于，SUT（system under test）不变，SUT的collaborator不再用真的object（之前的state verification，在要测试的方法调用完了之后我们通过检查collaborator的状态来验证方法是否正常被调用）。

Dummy objects are passed around but never actually used. Usually they are just used to fill parameter lists.
Fake objects actually have working implementations, but usually take some shortcut which makes them not suitable for production (an in memory database is a good example).
Stubs provide canned answers to calls made during the test, usually not responding at all to anything outside what's programmed in for the test. Stubs may also record information about calls, such as an email gateway stub that remembers the messages it 'sent', or maybe only how many messages it 'sent'.
Mocks are what we are talking about here: objects pre-programmed with expectations which form a specification of the calls they are expected to receive.

Dummy objects are passed around but never actually used. Usually they are just used to fill parameter lists.
Fake objects actually have working implementations, but usually take some shortcut which makes them not suitable for production (an in memory database is a good example).
Stubs provide canned answers to calls made during the test, usually not responding at all to anything outside what's programmed in for the test. Stubs may also record information about calls, such as an email gateway stub that remembers the messages it 'sent', or maybe only how many messages it 'sent'.
Mocks are what we are talking about here: objects pre-programmed with expectations which form a specification of the calls they are expected to receive.

Mock object只能用于behavior verification, Stub object则两个方向都可以，你也可以stub一些真的method，并在测试的最后检查stub object内部的状态。也可以仅仅使用stub来当做一个控制返回值的东西。

classical TDD争取使用真的东西，并且使用state verification。只有在真的东西不是很好用（awkward，比如mailService）时采用使用mock。mockist TDD

####想让一个object在多次调用时返回不同的值

如果使用连续的expect，返回的结果会和你指定的expect顺序相反

Instead, use the Expectation#returns method with multiple arguments to create multiple actions for a method

object = mock()
object.stubs(:expected_method).returns(1, 2).then.raises(Exception)
object.expected_method # => 1
object.expected_method # => 2
object.expected_method # => raises exception of class Exception1


##### Nested 方法
你知道你nested最终要调用的那个object的类型，就可以使用如下的方法来让所有的这个类型的instance的这个方法返回一个值
Person.any_instance.expects(:first_name).returns('david')

而不需要再创造一个mock的object注入到顶层的mockobject中，如这样

person = mock(:first_name => 'david')
product.expects(:person).return(person)

product.person #=> mockObject
product.person.first_name #=> david
