---
layout: post
title:  "复用类：代理"
categories: Java
tags: extends
author: fidemyuan
---
## 代理

将基类对象作为代理类的成员，而代理类有对应于基类的所有方法，各方法内使用基类对象成员调用基类的方法。

## 对比
1.组合 （让别人到家里来干活）

一个新类引进其他的类的对象，调用它的方法而实现代码重用的功能。


2.继承 （让自己的父母在家里干活）

子类继承父类的所有方法，实现代码重用。

3.代理  引进其他的类的对象，对其某些方法实现包装再暴露给外部。

	`public class test {  
	    public static void main(String[] args) {  
	        Demo2 d2=new Demo2();  
	        d2.print();  
	        //组合  
	        Demo1 d1=new Demo1();  
	        d1.method();  
	        //代理  
	        Demo3 d3=new Demo3();  
	        d3.stop();  
	    }  
	}  
	class Demo{  
	    Demo(){System.out.println("hello world!");}  
	    public static void stop(){System.out.println("Hello stop!");}  
	}  
	class Demo1{//组合机制  
	    public static void method(){  
	        Demo d=new Demo();//在新类中创建现有类的对象  
	    }  
	}  
	class Demo2 extends Demo{//继承机制，按照现有类来创建新类，无需改变现有类的形式，采用现有类的形式并且在其中添加新代码  
	    public static void print(){  
	        System.out.println("YES!");  
	    }  
	}  
	class Demo3{  
	    private Demo d=new Demo();  
	    public void stop(){//代理，既有继承功能 又有组合功能  
	        System.out.println("Hello 继承！");  
	        d.stop(); }}
	
	//输出结果：
	hello world!
	
	YES!
	
	hello world!
	
	hello world!
	
	hello 继承!
	
	hello stop!
		`
