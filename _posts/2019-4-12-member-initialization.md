---
layout: post
title:  "成员初始化"
categories: Java
tags: initialization（初始化）
author: fidemyuan
---

## 方法的局部变量初始化

在方法中初始化局部变量需要强制程序员赋一个初始值

例如：

	`void f(){
	    int i;
		i++;//这个时候会报错，没有初始化	 
 	 }`

## 类的成员变量初始化

在类的成员变量中定义的成员变量没有初始化，java会给其赋默认的值。

int类型变量默认初始值为0

float类型变量默认初始值为0.0f

double类型变量默认初始值为0.0

boolean类型变量默认初始值为false

char类型变量默认初始值为0(ASCII码)

long类型变量默认初始值为0

所有对象引用类型变量默认初始值为null，即不指向任何对象。

注意数组本身也是对象，所以没有初始化的数组引用在自动初始化后其值也是null。

## 如何为其赋值

直接在定义处为其进行赋值。

    `public class InitialValue{
	    boolean bool = true;
	    char ch = 'x';
	    byte b = 47;
	    short s = 0xff;
	    int i = 99;
	    long lng = 1;
	    float f = 3.14f;
	    double d =3.14159;
	    	Depth depth = new Depty();
	 }
     class Depth{}`

还可以调用某种方法来提供初值。这个方法可以带参数但是参数必须已被初始化了。

比如这样是可以的：

  	`public class methodInit{
		int i=f();
		int j=g(i);
		int f(){return 11};
		int g( int n){return n*10}
		}`

但是如果用于赋值的方法的参数没有被初始化就不行：

  	
	`public class methodInit2{
		//int j=g(i);	//这里对j的赋值中g(i)方法中的参数i还没有赋值，编译器会警告报错	
		int i=f();
		int f(){return 11};
		int g( int n){return n*10}
		}`