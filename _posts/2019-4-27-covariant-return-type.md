---
layout: post
title:  "协变返回类型"
categories: Java
tags: 覆盖重写
author: fidemyuan
---

## 什么是协变返回类型
导出类（子类）覆盖（即重写）基类（父类）方法时，返回的类型可以是基类方法返回类型的子类。

正常情况下子类覆盖重写父类是返回值类型，方法名，参数列表都一样才是覆盖重写。但是自SE1.5之后子类覆盖基类方法时，返回类型可以是某个基类方法返回类型的子类。

## 实例
	
	`
	  //协变返回类型,Thinking in Java p164
	  public class CovarianReturn {
	  
	      /**
	       * @param args
	       */
	     public static void main(String[] args) {
	          Mill m=new Mill();
	          Grain g=m.process();
	         System.out.println(g);
	         m=new WheatMill();
	         g=m.process();
	         System.out.println(g);
	     }
	 
	 }
	 class Grain{
	     public String toString(){
	         return "Grain";
	     }
	 }
	 class Wheat extends Grain{
	     public String toString(){
	         return "wheat";
	     }
	 }
	 class Mill{
	     Grain process(){
	         return new Grain();
	     }
	 }
	 class WheatMill extends Mill{
	     Wheat process(){
	         return new Wheat();
	     }
	 }
	//结果是：
	Grain
	Wheat
	`

如上面实例就是一个协变返回类型，看输出结果说明特成功覆盖了父类的方法。