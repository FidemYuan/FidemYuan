---
layout: post
title: HashMap1.7与HashSet
categories: JavaSE
tags: 集合
author: fidemyuan
---

## HashMap与HashSet的区别

从本质上来说HashSet是对HashMap做了一层包装，也就是说HashSet里面有一个HashMap,HashMap的Key就是HashSet。<br>

HashMap实现了Map接口存储键值对使用key计算HashCode，HashSet实现了Set接口存储对象使用成员对象计算HashCode，HashMap比HashSet快。


## HashMap的数据结构

![](https://github.com/fidemyuan/fidemyuan.github.io/blob/master/img-folder/HashMap.png)

如上图所示：1.7中HashMap的数据结构采用数组加链表的方式，数组每个元素存放链表结构，链表的每个节点又是名为entry的数组，entry数组中有三种属性：key、value、next。key和value就是HashMap的key、value,是节点的数据域。next属性就是链表的指针域。

## HashMap方法

**get()方法**<br>
get(Object key)方法根据指定的key值返回对应的value，该方法调用了getEntry(Object key)得到相应的entry，然后返回entry.getValue()。因此getEntry()是算法的核心。
算法思想是首先通过hash()函数得到对应bucket的下标，然后依次遍历冲突链表，通过key.equals(k)方法来判断是否是要找的那个entry。

	    `//getEntry()方法
	final Entry<K,V> getEntry(Object key) {
	    ......
	    int hash = (key == null) ? 0 : hash(key);
	    for (Entry<K,V> e = table[hash&(table.length-1)];//得到冲突链表
	         e != null; e = e.next) {//依次遍历冲突链表中的每个entry
	        Object k;
	        //依据equals()方法判断是否相等
	        if (e.hash == hash &&
	            ((k = e.key) == key || (key != null && key.equals(k))))
	            return e;
	    }
	    return null;
	}`

**put()方法**<br>
将指定的key, value对添加到map里。该方法首先会对map做一次查找，看是否包含该元组，如果已经包含则直接返回，查找过程类似于getEntry()方法；如果没有找到，则会通过addEntry(int hash, K key, V value, int bucketIndex)方法插入新的entry，插入方式为头插法。

	    `//addEntry()
	void addEntry(int hash, K key, V value, int bucketIndex) {
	    if ((size >= threshold) && (null != table[bucketIndex])) {
	        resize(2 * table.length);//自动扩容，并重新哈希
	        hash = (null != key) ? hash(key) : 0;
	        bucketIndex = hash & (table.length-1);//hash%table.length
	    }
	    //在冲突链表头部插入新的entry
	    Entry<K,V> e = table[bucketIndex];
	    table[bucketIndex] = new Entry<>(hash, key, value, e);
	    size++;
	}`

**remove()**<br>

remove(Object key)的作用是删除key值对应的entry，该方法的具体逻辑是在removeEntryForKey(Object key)里实现的。removeEntryForKey()方法会首先找到key值对应的entry，然后删除该entry（修改链表的相应指针）。查找过程跟getEntry()过程类似。

	    `//removeEntryForKey()
	final Entry<K,V> removeEntryForKey(Object key) {
	    ......
	    int hash = (key == null) ? 0 : hash(key);
	    int i = indexFor(hash, table.length);//hash&(table.length-1)
	    Entry<K,V> prev = table[i];//得到冲突链表
	    Entry<K,V> e = prev;
	    while (e != null) {//遍历冲突链表
	        Entry<K,V> next = e.next;
	        Object k;
	        if (e.hash == hash &&
	            ((k = e.key) == key || (key != null && key.equals(k)))) {//找到要删除的entry
	            modCount++; size--;
	            if (prev == e) table[i] = next;//删除的是冲突链表的第一个entry
	            else prev.next = next;
	            return e;
	        }
	        prev = e; e = next;
	    }
	    return e;
	}`

## HashSet

	    `//HashSet是对HashMap的简单包装
	public class HashSet<E>
	{
	    ......
	    private transient HashMap<E,Object> map;//HashSet里面有一个HashMap
	    // Dummy value to associate with an Object in the backing Map
	    private static final Object PRESENT = new Object();
	    public HashSet() {
	        map = new HashMap<>();
	    }
	    ......
	    public boolean add(E e) {//简单的方法转换
	        return map.put(e, PRESENT)==null;
	    }
	    ......
	}`