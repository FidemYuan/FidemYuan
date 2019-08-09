---
layout: post
title: 内部类与向上转型来隐藏实现
categories: JavaSE
tags: 内部类
author: fidemyuan
---

## 问题

现在想要实现隐藏内部类的具体细节，但是又需要获取此类？

首先可以用private修饰内部类，让其封闭，但是如何又可以任意调用此类呢。

在A中创建了一个private内部类B，添加了一个方法getB()
	
	`public class A {    
	    String name;    
	    private class B{
	        long identityId;
	        public void testB(){
	            System.out.println("I am B");
	        }
	    }
	    public B getB(){
	        return new B();
	    }
	}`

此时想尝试获取B类，是获取不到的，因为用private修饰了：

	`class test{
	    public static void main(String[] args) {
	        A a = new A();
	        A.B ab = a.getB(); //can't get A.B because of private
	    }
	}`


## 解决办法:让B实现一个接口，利用向上转型可获取B类，并隐藏了具体实现
	
	`interface C{
	    void testB();
	}`

让B类去实现他

	`public class A {
	    String name;
	    private class B implements C{  //changes here. Implements C
	        long identityId;                    
	        public void testB(){
	            System.out.println("I am B");
	        }
	    }
	    public B getB(){
	        return new B();
	    }
	}`

通过向上转型就调用getB()方法
	
	`class test{
	    public static void main(String[] args) {
	        A a = new A();
	        C ab = a.getB();  //we get a C 
	        ab.testB();   //use testB 
	    }
	}`

得到B类调用B实现接口C的那些方法，不知道B中的具体细节，隐藏了具体实现，达到了很好的封装性。