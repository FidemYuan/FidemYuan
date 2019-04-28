---
layout: post
title:  "组合和继承之间选择"
categories: Java
tags: 
author: fidemyuan
---

## 组合和继承的区别

组合跟继承都允许在新类中放置已有类的对象，但是组合是显式的需要在新类中创建，但是继承是隐式的，自动完成的。两个都能提高代码的复用率

组合技术通常用于想在新类中使用已有类的功能，而并非接口。一般在新类中嵌入已有类的对象，并用private进行修饰，别人看到的就是新类的接口。（当然在已有类成员对象都隐藏了具体实现的时候用public修饰让别人看到细节更容易理解端口也是可以的）
比如：

	`class Engine{
		public void start(){
			
		}
		public void rev(){
			
		}
		public void stop(){
			
		}
	}
	class Wheel{
		public void inflate(int psi){
			
		}
	}
	class Window{
		public void rollup(){
			
		}
		public void rolldown(){
			
		}
	}
	class Door{
		public Window window = new Window();
		public void open(){
			
		}
		public void close(){
			
		}
	}
	public class Car {
		public Engine ecgine = new Engine();
		public Wheel[] wheel = new Wheel[4];
		public Door
			left = new Door(),
			right = new Door();
		public Car(){
			for(int i = 0;i<4;i++)
				wheel[i] = new Wheel();
		}
		public static void main(String[] args) {
			// TODO Auto-generated method stub
			Car car = new Car();
			car.left.window.rollup();
			car.wheel[0].inflate(72);
		}
	 
	}`

继承技术一般用于想要使用现有类为了某种特殊需要而将其特殊化。
比如：

	`class Shape {
	    public void draw() {
	        System.out.println("draw a shape");
	    }
	
	    public void erase() {
	        System.out.println("erase");
	    }
	}
	
	class Square extends Shape {
	    @Override
	    public void draw() {
	        System.out.println("draw a Square");
	    }
	
	    public static void main(String[] args) {
	        Square s = new Square();
	        s.draw();
	        s.erase();
	    }
	}`

## 组合和继承相比的优缺点

一、相比于组合，继承有以下优点：

1、在继承中，子类自动继承父类的非私有成员(default类型视是否同包而定)，在需要时，可选择直接使用或重写。

2、在继承中，创建子类对象时，无需创建父类对象，因为系统会自动完成；而在组合中，创建组合类的对象时，通常需要创建其所使用的所有类的对象。

二、组合的优点：

1、在组合中，组合类与调用类之间低耦合；而在继承中子类与父类高耦合。

2、可动态组合。

总结：

虽然继承是OOP的一大特性，但很多时候并不推荐使用，因为它常常容易使结构复杂化，容易出错。因此，除非我们确信使用继承会使程序效率最高，否则，不考虑使用它。

补充：子类不能继承父类的私有属性指的是：

在一个子类被创建的时候，首先会在内存中创建一个父类对象，然后在父类对象外部放上子类独有的属性，两者合起来形成一个子类的对象。所以所谓的继承使子类拥有父类所有的属性和方法其实可以这样理解，**子类对象确实拥有父类对象中所有的属性和方法，但是父类对象中的私有属性和方法，子类是无法访问到的，只是拥有，但不能使用。就像有些东西你可能拥有，但是你并不能使用。**