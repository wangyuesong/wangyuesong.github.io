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
 
Set就记住一个HashSet就好了，hashSet的增加元素也是add，查找元素是否存在使用的是contains


下面是它几个实现类的解释

HashSet is Implemented using a hash table. Elements are not ordered. The add, remove, and contains methods have constant time complexity O(1).

TreeSet is implemented using a tree structure(red-black tree in algorithm book). The elements in a set are sorted, but the add, remove, and contains methods has time complexity of O(log (n)). It offers several methods to deal with the ordered set like first(), last(), headSet(), tailSet(), etc.

LinkedHashSet is between HashSet and TreeSet. It is implemented as a hash table with a linked list running through it, so it provides the order of insertion. The time complexity of basic methods is O(1).

总的来说，HashSet不能保证按照插入顺序存储元素，TreeSet可以但是效率都是logN,可以用LinkedHashSet来代替HashSet，它的底层实现还是一个HashTable，不过有一个链表在所有元素之间穿插来记录顺序

##Map

![Yanghu](/assets/2015-12-26-Data Structure类问题/Map类图.png)

Map一般只用HashMap就够了，HashTable是线程安全的，但是效率低

TreeMap和LinkedHashMap和Set差不多

大概记录一下Lintcode上这类题目的解法：
Merge K sorted Array: PriorityQueue,自建数据结构表示Array以及当前Array的最大值，PQ中最多存在K个element，要merge这个n个element的array需要向queue中add n次，每次都是logK的效率，所以效率是NLogK

Rehashing: 很简单的实现，使用数组加链表实现了hashTable

Longest Consecutive Sequence: 在一个未排序的数组中找到最长的连续数列。使用数据结构HashMap来记录当前这个元素是否被检查过了以及方便o（1）的元素查找，遍历nums中的元素，如果检查过了就跳过，没检查过就向hashMap中寻找两侧的元素并且统计长度，断了后局部max和全局max比较

Merge k sorted List: 同样是PriorityQueue，这次连数据结构都不用建了，但是要写一个Comparator传给PQ，作为ListNode的比较器

Implement Queue By Two Stacks: 一个Stack存正序（相对于Queue的正序），一个Stack存反序，add时向反序的Stack中push，此时反序Stack最顶端是最新push进来的元素。然后poll时，如果正序stack是空，将反序stack全pop到正序stack中，然后再正序stack pop一个出来

MinStack: 保存两个栈，一个栈用于保存真正元素，另一个保存当前最小

Ugly Number:有点像数学题，还是要用PriorityQueue和一个HashSet（记录该元素是否已经被加入过Queue），3，5，7，9，15，21，25。。。多写几个应该就发现规律，注意使用Long

发现HashMap（Set）在记录某个元素是否之前被遍历过很有用处
