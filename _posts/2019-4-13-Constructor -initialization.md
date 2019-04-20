---
layout: post
title:  "构造器初始化"
categories: Java
tags: initialization（初始化）
author: fidemyuan
---

## 构造器初始化时的执行顺序

在类的内部，变量定义的先后顺序决定了初始化的顺序，即使变量定义散布于方法定义之间，他们仍然会在任何方法（包括构造器）被调用之前得到初始化。

在创建一个类的对象时：new 对象（），首先会在堆上为此对象分配足够的存储空间，这个对象的基本类型默认值为0，引用默认就是null。然后通过构造方法再去初始化具体的值。

	`
	class Counter {
		int i;
		Counter() {
			//i = 7;
		}
	}
	public class Test {
		public static void main(String[] args) {
			System.out.println(new Counter().i);
		}
	}`
上面这段代码把i=7注释掉，结果就是0，说明成员变量一开始是有值的，为0。说明构造器初始化之前，对象的成员变量就已经先初始化了。得到上面的内容。

一个复杂的例子：

	`public class OrderOfInitialization {
	 
		public static void main(String[] args) {
			House h = new House();
			h.f();
		}
	}
	 
	class Window {
		Window(int marker) {
			System.out.println("window(" + marker + ")");
		}
	}
	 
	class House {
		Window w1 = new Window(1);
		House() {
			System.out.println("House()");
			w3 = new Window(33);
		}
		Window w2 = new Window(2);
		void f() {
			System.out.println("f()");
		}
		Window w3 = new Window(3);
	}
	 
	/*
	 * output:
	window(1)
	window(2)
	window(3)
	House()
	window(33)
	f()
	 */
上面程序的执行结果，可以看出在构造器执行之前对象的成员变量是先执行的，即使分散在构造器方法之间（不管前后）。而这些成员变量的初始化顺序与其先后顺序有关。

##  静态数据的初始化

无论创建多少个对象，静态数据都只占用一份存储区域，static关键词不能作用于成员变量。如果是静态基本类型域初始值就是0，如果是对象引用初始值是null。

初始化的顺序是：先加载静态对象，然后再是非静态对象，再执行构造器。

例如下面程序结果：
	`class Bowl {
		Bowl(int marker) {
			System.out.println("Bowl(" + marker + ")");
		}
		void f1(int marker) {
			System.out.println("f1(" + marker + ")");
		}
	}
	 
	class Table {
		static Bowl bowl1 = new Bowl(1);
		Table() {
			System.out.println("Table()");
			bowl2.f1(1);
		} 
		void f2(int marker) {
			System.out.println("f2(" + marker + ")");
		}
		static Bowl bowl2 = new Bowl(2);
	}
	 
	class Cupboard {
		Bowl bowl3 = new Bowl(3);
		static Bowl bowl4 = new Bowl(4);
		Cupboard() {
			System.out.println("cupboard()");
			bowl4.f1(2);
		}
		void f3(int marker) {
			System.out.println("f3(" + marker + ")");
		}
		static Bowl bowl5 = new Bowl(5);
	}
	public class StaticInialization {
	 
		public static void main(String[] args) {
			System.out.println("Creating new cupboard in main()");
			new Cupboard();
			System.out.println("Creating new cupboard in main()");
			new Cupboard();
			table.f2(1);
			cupboard.f3(1);
		}
		
		static Table table = new Table();
		static Cupboard cupboard = new Cupboard();
		Bowl b = new Bowl(1); //自己额外添加的
	}

	结果：

	Bowl(1)
	Bowl(2)
	Table()
	f1(1)
	Bowl(4)
	Bowl(5)
	Bowl(3)
	cupboard()
	f1(2)
	Creating new cupboard in main()
	Bowl(3)
	cupboard()
	f1(2)
	Creating new cupboard in main()
	Bowl(3)
	cupboard()
	f1(2)
	f2(1)
	f3(1)`
## 非显示的静态初始化顺序（静态代码块）

当程序中出现代码块时，尽管看起来像是方法，但是其初始化过程与成员变量初始化过程是一样的。类一加载就初始化默认值。
	
	static{
	......
		}

静态代码块，在首次生成对象，或者首次访问类的静态成员变量或静态方法时执行一次。此后都不会再执行。

## 非静态实例初始化顺序（非静态代码块）
一般用{  }包裹起来，没有static关键词。创建对象时实例初始化聚在，在构造器之前执行。

非静态代码块，每次生成新的对象时都会执行一次。

## 总结：对象创建时，各成员的执行（加载）过程
比如说有一个Music类，还没执行的时候，是被编译成Music.class字节码文件放在硬盘中。

1.现在程序执行需要创建Music对象，或者Music类的静态静态方法(或者构造方法)/静态域被访问时，java解释器会去查找类路径，定位Music.class字节码文件。
2.然后加载Music.class文件，有关静态初始化的所有动作都会去执行，只在首次执行一次。
3.如果是new Music()加载Music文件时会在堆上为Music对象分配足够的空间,让这块空间被清零。Music对象接下来可以使用这块内存，对象中的基本类型变量都设为默认值（数字是0，布尔、字符对应的.....）,对象引用设为null。
4.再执行所有字段（成员变量）的初始化动作。
5.执行构造器，初始化对象