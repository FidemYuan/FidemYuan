---
layout: post
title: 常见的数据结构
categories: JavaSE
tags: 数据结构	
author: fidemyuan
---

## 常见的数据结构有哪些

数组，栈，链表，队列，树，图，堆，散列表

## 数组

数组是可以再内存中连续存储多个元素的结构，在内存中的分配也是连续的，数组中的元素通过数组下标进行访问，数组下标从0开始。

**数组的优缺点适用场景**

优点： <br>
1、按照索引查询元素速度快 <br>
2、按照索引遍历数组方便<br>

缺点： <br>
1、数组的大小固定后就无法扩容了 <br>
2、数组只能存储一种类型的数据 <br>
3、添加，删除的操作慢，因为要移动其他的元素。<br>

适用场景： <br>
频繁查询，对存储空间要求不大，很少增加和删除的情况。<br>

## 栈

栈是一种特殊的线性表，仅能在线性表的一端操作，栈顶允许操作，栈底不允许操作。 栈的特点是：先进后出，或者说是后进先出，从栈顶放入元素的操作叫入栈，取出元素叫出栈。 <br>

越先放进去的东西越晚才能拿出来，所以，栈常应用于实现递归功能方面的场景。<br>

## 队列

队列与栈一样，也是一种线性表，不同的是，队列可以在一端添加元素，在另一端取出元素，也就是：先进先出。从一端放入元素的操作称为入队，取出元素为出队。<br>

使用场景：因为队列先进先出的特点，在多线程阻塞队列管理中非常适用。<br>

## 链表

链表是物理存储单元上非连续的、非顺序的存储结构，数据元素的逻辑顺序是通过链表的指针地址实现，每个元素包含两个结点，一个是存储元素的数据域 (内存空间)，另一个是指向下一个结点地址的指针域。根据指针的指向，链表能形成不同的结构，例如单链表，双向链表，循环链表等。<br>

**优缺点适用场景：**

链表的优点： <br>
链表是很常用的一种数据结构，不需要初始化容量，可以任意加减元素； <br>
添加或者删除元素时只需要改变前后两个元素结点的指针域指向地址即可，所以添加，删除很快；<br>

缺点：<br> 
因为含有大量的指针域，占用空间较大； <br>
查找元素需要遍历链表来查找，非常耗时。<br>

适用场景： <br>
数据量较小，需要频繁增加，删除操作的场景<br>

## 树

树是一种数据结构，它是由n（n>=1）个有限节点组成一个具有层次关系的集合。<br>

二叉树是树的特殊一种，具有如下特点：<br>

1、每个结点最多有两颗子树，结点的度最大为2。 <br>
2、左子树和右子树是有顺序的，次序不能颠倒。 <br>
3、即使某结点只有一个子树，也要区分左右子树。<br>

**二叉树是折中的方案**

二叉树是一种比较有用的折中方案，它添加，删除元素都很快，并且在查找方面也有很多的算法优化，所以，二叉树既有链表的好处，也有数组的好处，是两者的优化方案，在处理大批量的动态数据方面非常有用。<br>

## 哈希表

散列表，也叫哈希表，是根据关键码和值 (key和value) 直接进行访问的数据结构，通过key和value来映射到集合中的一个位置，这样就可以很快找到集合中的对应元素。<br>

### 大致原理
把Key通过一个固定的算法函数既所谓的哈希函数转换成一个整型数字，然后就将该数字对数组长度进行取余，取余结果就当作数组的下标，将value存储在以该数字为下标的数组空间里，这种存储空间可以充分利用数组的查找优势来查找元素，所以查找的速度很快。<br>

## 堆

堆是一种比较特殊的数据结构，可以被看做一棵树的数组对象，具有以下的性质：<br>

堆中某个节点的值总是不大于或不小于其父节点的值；<br>

堆总是一棵完全二叉树。<br>
将根节点最大的堆叫做最大堆或大根堆，根节点最小的堆叫做最小堆或小根堆。<br>

ki <= k2i,ki <= k2i+1)称为：小根堆
(ki >= k2i,ki >= k2i+1)称为：大根堆

## 图

图是一组以网络形式相互连接的节点。节点也称为顶点。