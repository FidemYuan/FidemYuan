---
layout: post
title: 集合的分类
categories: JavaSE
tags: 数组与集合
author: fidemyuan
---

## 集合分类图

![](https://github.com/fidemyuan/fidemyuan.github.io/blob/master/img-folder/Conllection.png)


##Conllection与Map

Collection是最基本的集合接口，声明了适用于JAVA集合（只包括Set和List）的通用方法。 Set 和List 都继承了Conllection；Set具有与Collection完全一样的接口，因此没有任何额外的功能，不像前面有两个不同的List。实际上Set就是Collection,只 是行为不同。(这是继承与多态思想的典型应用：表现不同的行为。)Set不保存重复的元素(至于如何判断元素相同则较为负责) 

 **Map没有继承于Collection接口** 从Map集合中检索元素时，只要给出键对象，就会返回对应的值对象。 	
## Conllection与Map的区别

容器内每个为之所存储的元素个数不同。
Collection类型者，每个位置只有一个元素。
Map类型者，持有 key-value pair，像个小型数据库。

## Conllection系

 collection作为根接口，提供了对于collection系容器类的最基本存取方式。其下子接口List和Set借口<br>

###List
将以特定次序存储元素。所以取出来的顺序可能和放入顺序不同。可以重复<br>

**1.ArrayList**<br>
ArrayList是一个动态数组的结构，不是线程安全的<br>
**2.LinkedList**<br>
LinkedList 是一个继承于AbstractSequentialList的双向链表。<br>
**3.Vector**<br>
Vector也是数组的一个数据结构,是线程安全的。

### Set  
不能含有重复的元素<br>

**1.HashSet：**<br>
无序集合，存储和取出的顺序不同，没有索引，不存储重复元素。由哈希表（实际是HashMap实例）支持。此类允许使用null元素。<br>
**2.TreeSet<br>**<br>
是一个有序的集合，它的作用是提供有序的Set集合<br>

## Map系

**1.HashMap**<br>
是基于Hash表和Map来实现的<br>
**2.HashTable**<br>
**3.TreeMap**<br>

