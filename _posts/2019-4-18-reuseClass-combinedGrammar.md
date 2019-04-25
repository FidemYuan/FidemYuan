---
layout: post
title:  "复用类：组合语法"
categories: Java
tags: package
author: fidemyuan
---

## 复用代码

复用代码是java引人注目的功能之一
在不改变现有程序的基础之上有两种方法可以实现复用类以进行代码的复用。第一种就是组合语法，另外一种是继承。
## 组合语法

只需在新的类中产生现有类的对象。由于新的类是由现有类的对象所组成，所以这种方法称为组合。该方法只是复用了现有程序代码的功能。

## 实现过程
	`
	public class WaterSource {
	private String s;
 
	public WaterSource() {
		System.out.println("WaterSource()");
		s = "Constructed";
	}
 
	@Override
	public String toString() {
		return s;
	 }
	}


 
	public class SprinklerSystem {
	private String valve1, valve2, valve3, valve4;
	private WaterSource source = new WaterSource();
	private int i;
	private float f;
 
	@Override
	public String toString() {
		return "valve1=" + valve1 + " valve2=" + valve2 + " valve3=" + valve3
				+ " valve4=" + valve4 + "\n" + "i=" + i + " f=" + f + "\n"
				+ "source=" + source;
	}
 
	public static void main(String[] args) {
		SprinklerSystem sprinklerSystem = new SprinklerSystem();
		System.out.println(sprinklerSystem);
	}

	//结果：
	WaterSource()
	valve1=null valve2=null valve3=null valve4=null
	i=0 f=0.0
	source=Constructed

	`

代码解析：
  	
每一个对象都是继承于Object对象，那么每个非基本类型的对象都有一个toString()方法，可以对这个方法进行重写，如上面代码中所示。
在SprinklerSystem.toString()的表达式中：

`"source=" + source`

编辑器将会得知你想要将一个String对象（"source="）同WaterSource相加。由于只能将一个String对象和另一个String对象相加，因此编译器会告诉你：“我将调用toString()，把source转换成为一个String！”这样做之后，它就能够将两个String连接到一起并将结果传递给System.out.println()。
可以看出source对象是没有在新类中写toString方法的，编译器会自动调用其toString方法，于是就达到了代码复用的目的。
每当想要使所创建的类具备这样的行为时，仅需要编写一个toString()方法即可。
