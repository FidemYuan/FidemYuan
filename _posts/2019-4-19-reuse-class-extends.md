---
layout: post
title:  "复用类：继承"
categories: Java
tags: extends
author: fidemyuan
---

## 什么是继承

创建一个类的时候其实总是在继承，除非明确的指出要从其他类中继承，否则都是默认的从java的标准根类Object继承。申明一个类继承另一个类，就会自动得到基类中的所有域和方法。

## 格式

类主体花括号之前extends+基类名称

为了继承，一般的规则是将所有的域（数据成员）都指定为 private，将所有方法指定为 public 
## 用法

可以覆盖重写基类中的方法，或者另外添加新的方法

## 初始化基类

在申明一个类继承一个基类的时候。不是单纯的复制接口，增加额外的方法和域，而是创建一个导出类的时候，这个导出类中包含了一个基类对象。

那么既然在导出类内部创建了基类，那么就涉及到需要初始化基类。

### 默认构造器
在导出类的构造器中调用基类的构造器来初始化基类。如果导出类中没有定义构造器，那么编译器会默认赠送一个无参的构造器去调用基类。

### 参数构造器

但是如果想要传参数来初始化类对象，必须要构造合适的带参数的构造器。在继承关系中，传参数时在导出类的构造器肯定要调用基类的构造器。此时用关键词super来编写父类构造器。

	`class Person { 
	    public static void prt(String s) { 
	       System.out.println(s); 
	    } 
	   
	    Person() { 
	       prt("父类·无参数构造方法： "+"A Person."); 
	    }//构造方法(1) 
	    
	    Person(String name) { 
	       prt("父类·含一个参数的构造方法： "+"A person's name is " + name); 
	    }//构造方法(2) 
	} 
	    
	public class Chinese extends Person { 
	    Chinese() { 
	       super(); // 调用父类构造方法（1） 
	       prt("子类·调用父类”无参数构造方法“： "+"A chinese coder."); 
	    } 
	    
	    Chinese(String name) { 
	       super(name);// 调用父类具有相同形参的构造方法（2） 
	       prt("子类·调用父类”含一个参数的构造方法“： "+"his name is " + name); 
	    } 
	    
	    Chinese(String name, int age) { 
	       this(name);// 调用具有相同形参的构造方法（3） 
	       prt("子类：调用子类具有相同形参的构造方法：his age is " + age); 
	    } 
	    
	    public static void main(String[] args) { 
	       Chinese cn = new Chinese(); 
	       cn = new Chinese("codersai"); 
	       cn = new Chinese("codersai", 18); 
	    } 
	}

	//运行结果：
	
	父类·无参数构造方法： A Person.
	子类·调用父类”无参数构造方法“： A chinese coder.
	父类·含一个参数的构造方法： A person's name is codersai
	子类·调用父类”含一个参数的构造方法“： his name is codersai
	父类·含一个参数的构造方法： A person's name is codersai
	子类·调用父类”含一个参数的构造方法“： his name is codersai
	子类：调用子类具有相同形参的构造方法：his age is 18
			`

