---
layout: post
title: 完全解耦
categories: JavaSE
tags: 接口
author: fidemyuan
---

## 完全解耦

只要一个方法操作的是类而非接口。那么你就只能使用这个类及其子类。如果你想要将这个方法应用于不在此继承结构中的某个类，那么就不行了。接口可以在很大程度上放宽这种限制。

## 策略设计模式

创建一个能够根据所传递的参数对象的不同而具有不同行为的方法。这类方法包含所要执行的算法中固定不变的部分。而“策略”包含变化的部分。策略就是传递进去的参数对象，它包含要执行的代码。

	`class Processor{
	  public String name(){
	         return getClass().getSimpleName();      
	    }
	  Object process(Object input){ return input;}      
	}
	
	class Upcase extends Processor{
	   String process(Object input){ return ((String)input).toUpperCase();}  
	}
	class Downcase extends Processor{
	   String process(Object input){ return ((String)input).toLowerCase();}  
	}
	
	public class Apply{
	　　public static void process(Processor p,Object s){
	　　　　System.out.print(“Using Processor”+p.name());
	　　　　System.out.print(p.process(s));
	　　}
	　　String s = "Abcd";
	　　public static void main(String[] args){
	　　　　process(new Upcase(),s);
	　　　　process(new Downcase(),s)
	　　}
	}/Output:
	Using Processor Upcase
	ABCD
	Using Processor DownCase
	abcd`

## 适配器模式

适配器中的代码将接受你所拥有的接口，并产生你所需要的接口。

	`class FilterAdapter impletemts Processor{
	    Filter filter； 
	    public FilterAdapter（Filter filter）{
	         this.filter=filter;
	    }         
	}    `