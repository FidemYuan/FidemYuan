---
layout: post
title: 集合的数据结构
categories: JavaSE
tags: 集合的数据结构	
author: fidemyuan
---

##Conllection(单列集合)

### List(有序，可重复)

**ArrayLit**
底层数据结构是数组，查询快，增删慢
线程不安全，效率高

**Vector**
底层数据结构是数组，查询快，增删慢
线程安全，效率低

**LinkedList**
底层数据结构是链表,查询慢,增删快
线程不安全,效率高

### Set(无序,唯一)

**HashSet**

底层数据结构是哈希表。<br>
哈希表依赖两个方法：hashCode()和equals()<br>

（一般规则:对象equals 是true的话，hashCode需要相同，但是hashCode相同的对象不一定equals，这就是所谓的冲突现象，但是有不同的冲突解决方法。你的hashCode()设计的好的话冲突也就小了。比如楼上给出的超出int范围之后这种hashCode()实现，对象肯定是无数的，但是hash实现是有限的呢,所以冲突了。）<br>
执行顺序：
首先判断hashCode()值是否相同<br>
 是：继续执行equals(),看其返回值<br>
是true:说明元素重复，不添加<br>
是false:就直接添加到集合<br>
否：就直接添加到集合<br>
最终：
自动生成hashCode()和equals()即可<br>

**LinkedHashSet**

底层数据结构由链表和哈希表组成。<br>
由链表保证元素有序。<br>
由哈希表保证元素唯一。<br>

**TreeSet**

底层数据结构是红黑树。(是一种自平衡的二叉树)<br>
如何保证元素唯一性呢?<br>
根据比较的返回值是否是0来决定<br>
如何保证元素的排序呢?<br>
两种方式<br>
自然排序(元素具备比较性)<br>
让元素所属的类实现Comparable接口<br>
比较器排序(集合具备比较性)<br>
让集合接收一个Comparator的实现类对象<br>

## Map(双列集合)
A:Map集合的数据结构仅仅针对键有效，与值无关。<br>
B:存储的是键值对形式的元素，键唯一，值可重复。<br>
        
### HashMap
底层数据结构是哈希表。线程不安全，效率高<br>
哈希表依赖两个方法：hashCode()和equals()<br>

执行顺序：<br>
首先判断hashCode()值是否相同<br>
是：继续执行equals(),看其返回值<br>
是true:说明元素重复，不添加<br>
是false:就直接添加到集合<br>
否：就直接添加到集合<br>
最终：<br>
自动生成hashCode()和equals()即可<br>

### LinkedHashMap

底层数据结构由链表和哈希表组成。<br>
由链表保证元素有序。<br>
由哈希表保证元素唯一。<br>

### Hashtable

底层数据结构是哈希表。线程安全，效率低<br>
哈希表依赖两个方法：hashCode()和equals()<br>
执行顺序：<br>
首先判断hashCode()值是否相同<br>
是：继续执行equals(),看其返回值<br>
是true:说明元素重复，不添加<br>
是false:就直接添加到集合<br>
否：就直接添加到集合<br>
最终：<br>
自动生成hashCode()和equals()即可<br>

## TreeMap

底层数据结构是红黑树。(是一种自平衡的二叉树)<br>
如何保证元素唯一性呢?<br>
根据比较的返回值是否是0来决定<br>
如何保证元素的排序呢?<br>
两种方式<br>
自然排序(元素具备比较性)<br>
让元素所属的类实现Comparable接口<br>
比较器排序(集合具备比较性)<br>
让集合接收一个Comparator的实现类对象<br>

## 使用哪种集合
  
 是否是键值对象形式:<br>
是：Map<br>
键是否需要排序:<br>
是：TreeMap<br>
否：HashMap<br>
不知道，就使用HashMap。<br>
		            
否：Collection<br>
元素是否唯一:<br>
是：Set<br>
元素是否需要排序:<br>
是：TreeSet<br>
否：HashSet<br>
不知道，就使用HashSet<br>
		                    
否：List<br>
要安全吗:<br>
是：Vector<br>
否：ArrayList或者LinkedList<br>
增删多：LinkedList<br>
查询多：ArrayList<br>
不知道，就使用ArrayList<br>
不知道，就使用ArrayList<br>