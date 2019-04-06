---
layout: post
title:  "赋值操作符"
categories: Java
tags: java 
author: fidemyuan
---

##  什么叫操作符

操作符，操作数据的符号，作用于操作数。在底层，在java中的数据都是通过操作符来操作的。操作符接受一个或多个参数，并生成新值，参数的形式不同于方法中的参数，比如a++。

##  赋值操作符

赋值的操作符：`=`，是将=右边的值复制给左边。右边可以是变量、常数、表达式，只要是一个值就行，左边不能是常量。

注意：赋值要区分基本类型跟对象引用的赋值两者是有区别的，如下：

###  对基本类型的赋值

基本类型的赋值是直接将一个值赋值到了另一个地方。比如：
	
 	`int a=3;
     int b=4;
	 a=b;//将b中存储的值赋值到a中存储
			`
这个时候b的值就赋值给了a这个时候a，b是独立的，之后b中的值改变了也不影响a。

### 对象之间的赋值

之前分享过，在java中操作对象是，是操作的对象的引用。那么c=d,是讲对象的"引用"d赋值给了另一个对象的“引用”c，而不是将实际对象中的值赋值了过去。所以此时c、d都指向了d原来指向的那个对象。换句话说，c、d作为对象的引用存储的是对象的地址值，赋值的时候是讲d的地址值赋值给了c。比如下面代码运行的结果：

`
	class Tank {
  		int level;
	  }

	public class test{
	public static void main(String[] args){
	Tank  t1 =new Tank();
	Tank  t2 =new Tank();
    t1.level= 9;
	t2.lecel=47;
	
	System.out.println("t1.level:"+t1.level+"t2.level:"+t2.level);

      t1=t2;
	System.out.println("t1.level:"+t1.level+"t2.level:"+t2.level);

    t1.level=27;

	System.out.println("t1.level:"+t1.level+"t2.level:"+t2.level)
      }
    
     }

    /*结果就是：
        9   47
        47  47
        27   27*/  
					`


