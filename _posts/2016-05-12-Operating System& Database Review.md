---
layout: post
category: Operating System
title: Operating System & Database Review
tagline: by Archer
tags:
- Operating System
- Review

---

1.Page table
virtual add-> physical address

CPU的Memory Management Unit(MMU) 存储Translation Lookaside Buffer(TLB) 作为page table在cpu中的缓存

Translation可能会出现failure，一种是由于programming error,会产生segmentation fault.还有一种就是没有存在于physical memeory，被交换到swap partition去了

2.Index

数据库的数据存到storage device的时候是按照block来存的，block的读写都是整体存取的，而block的排列方法是linked list，不需要连续存储

对于对某个字段未index的table，如果想要找某一个field=value的entry，需要从头到尾搜索。create index 在某一个field可以让我们通过二分的方法来对field=某个值 的某条记录进行查找（或者是区域查找）。

但是index需要很多的disk space，Index同样也要作为table存在MyISAM engine中。

假设这样的一个database schema

Field name       Data type      Size on disk
id (Primary key) Unsigned INT   4 bytes
firstName        Char(50)       50 bytes
lastName         Char(50)       50 bytes
emailAddress     Char(100)      100 bytes

假设一个block可以存1024 bytes，每一个block就可以存5条这样记录，如果有50000条records则需要10000个block

在上面的linear search如果没有重复的元素，平均需要查找10000/2=5000个block，如果有重复则要查找10000个。但是如果是index过的，
我们可以得到这样的一张ID index表

Field name       Data type      Size on disk
id        Char(50)       4 bytes
(record pointer) Special        4 bytes

整个表每条记录只要占8 bytes，假设每一个记录都有这么一个index，总共index表所占的block数要比原记录小得多。

而整个ID index表也是按照id来排序的，可以使用二分法查找。

index对于多cardinality的字段可以增加查找效率，如果重复值很多的字段则会退化。

3.Clustered index 和 非clustered index

clutered index只能有一个，因为设置了cluster index就会让整个表按照cluster index 列的大小来排列。非cluster的index会有一张上面的表，因此可以有很多个

cluster indexed的表会在插入时影响时间，因为可能导致rearrange

4.行存储和列存储

对于relational db来讲，行存储和列存储的区别不是特别大，行存储按行存放数据，对于

RowId	EmpId	Lastname	Firstname	Salary
001	10	Smith	Joe	40000
002	12	Jones	Mary	50000
003	11	Johnson	Cathy	44000
004	22	Jones	Bob	55000


这样一张表（rowId是数据库internal自动分配的，和用户提供的Id不一样），行存储会这样

001:10,Smith,Joe,40000;
002:12,Jones,Mary,50000;
003:11,Johnson,Cathy,44000;
004:22,Jones,Bob,55000;

存。这种行存储的方式对于存取一个或者一系列object的应用很有效，比如商品展示，每次都是取一条记录然后展示其中的所有字段，但是对于在整张表上的操作，比如salary求和，行存储的数据库则需要遍历整张表，

遍历到所有block，这样locality of reference 就会变差。可以用index来加速，这样：

001:40000;
003:44000;
002:50000;
004:55000;

但是这样会增加数据库的overhead

column-oriented的数据库是这样

10:001,12:002,11:003,22:004;
Smith:001,Jones:002,Johnson:003,Jones:004;
Joe:001,Mary:002,Cathy:003,Bob:004;
40000:001,50000:002,44000:003,55000:004;

这里并不是每一个column就相当于row-based database中的一个index。row based index是从internalId->data的，而column based是从data->Internal Id

这样对于仅仅针对某个字段的查找就十分有效率
