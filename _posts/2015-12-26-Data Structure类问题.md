---
layout: post
category: 面试
title: Data Structure类问题
tagline: by Archer
tags:
- 面试
- Lintcode
- Data Stucture

---

在开始总结Data Structure的题目之前，先对Java中的DataStructure做个总结

Java中的集合类主要由两个接口派生而出：Collection和Map

##Collection 

Collection下又有几个大的接口Set，List和Queue

![Yanghu](/assets/2015-12-26-Data Structure类问题/List类图.png)

####List

List代表支持随机存取的集合类，典型的三大实现类有ArrayList，LinkedList和Vector

Vector是线程安全的，但是效率低下，在不考虑线程安全的情况下可以多用ArrayList。Stack继承Vector，设计存在问题，一般推荐用LinkedList来代替Stack,LinkedList中就有push/pop这些操作

List的主要方法有get（index），set（index，value），indexOf（value）以及subList（）

####Queue

队列集合类，还有一个子类Dequeue是双向队列，LinkedList和PriorityQueue实现了Queue接口，在做题时应该一般使用LinkedList来作Queue的实现

Queue接口有如下几个方法：

add(element) throw Exception/offer(element)在尾部加入元素,element()/peek()取头元素,poll()/remove() throw Exception取出头元素

PriorityQueue的增删都是LogN，取当前最小(大)元素是1

####Set
 
Set就记住一个HashSet就好了


下面是它几个实现类的解释

HashSet is Implemented using a hash table. Elements are not ordered. The add, remove, and contains methods have constant time complexity O(1).

TreeSet is implemented using a tree structure(red-black tree in algorithm book). The elements in a set are sorted, but the add, remove, and contains methods has time complexity of O(log (n)). It offers several methods to deal with the ordered set like first(), last(), headSet(), tailSet(), etc.

LinkedHashSet is between HashSet and TreeSet. It is implemented as a hash table with a linked list running through it, so it provides the order of insertion. The time complexity of basic methods is O(1).

总的来说，HashSet不能保证按照插入顺序存储元素，TreeSet可以但是效率都是logN,可以用LinkedHashSet来代替HashSet，它的底层实现还是一个HashTable，不过有一个链表在所有元素之间穿插来记录顺序

##Map

![Yanghu](/assets/2015-12-26-Data Structure类问题/Map类图.png)

Map一般只用HashMap就够了，HashTable是线程安全的，但是效率低

TreeMap和LinkedHashMap和Set差不多
