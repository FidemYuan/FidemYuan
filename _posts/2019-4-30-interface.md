---
layout: post
title:  接口
categories: JavaSE
tags: 接口
author: fidemyuan
---

## 什么叫接口

所谓接口，从字面上来理解是一种对外提供的统一标准，需要来实现着统一的接口，是对类的标准制定。

接口中的变量全部都是final、public、static的，方法全部都是抽象方法可以说是100%的抽象类。一个类实现接口，要重写接口中全部的抽象方法否则是一个抽象类，不是抽象类就必须重写全部的抽象方法。

## 接口的声明与实现

interface关键字：

	`interface in1
	{
	//public、static and final
	final int a=10;
	
	//public and abstract
	void display();
	
	}
	
	`
实现：

	`class testClass implements in1{
		public void  display(){
	
	System.out.println("Geek")
	}
	
	
	}`


## 使用接口时的注意

1.不能直接去实例化一个接口，因为接口中的方法都是抽象的，是没有方法体的。可以使用接口类型的引用指向一个实现了该接口的对象，并且可以调用这个接口中的方法。

2.一个类可以实现不止一个接口。

3.一个接口可以继承于另一个接口，或者另一些接口，接口也可以继承，并且可以多继承。

4.一个类如果要实现某个接口的话，那么它必须要实现这个接口中的所有方法。

5.接口中所有的方法都是抽象的和public的，所有的属性都是public,static,final的。

6.接口用来弥补类无法实现多继承的局限。

7.接口也可以用来实现解耦。

