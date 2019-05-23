---
layout: post
title: 接口与工厂
categories: JavaSE
tags: 接口
author: fidemyuan
---


## 什么是工厂模式

创建一个工厂对象，在工厂对象上调用的某种方法,生成接口的某个实现的对象,通过这种方式,可以与接口的实现分离,使得我们可以透明地将某个实现替换为另一个实现在创建对象时不会对客户端暴露创建逻辑，并且是通过使用一个共同的接口来指向新创建的对象。

## 工厂模式的优点

1.解耦：调用方不用负责对象的创建，只需要使用，明确各自的职责

2.维护方便：后期如果创建对象时需要修改代码，也只需要去工厂方法中修改，易拓展

工厂模式细分为：简单工厂，工厂模式，抽象工厂

## 使用思路

	`//创建一个接口，利用接口的多实现，让接下来的类都实现接口，后面用接口来指向工厂创建的不同类对象
	public interface Shape {
	   void draw();
	}

    //Rectangle类
	
	public class Rectangle implements Shape {
	 
	   @Override
	   public void draw() {
	      System.out.println("Inside Rectangle::draw() method.");
	   }
	}
	//Square类
	public class Square implements Shape {
	 
	   @Override
	   public void draw() {
	      System.out.println("Inside Square::draw() method.");
	   }
	}

	//Circle类	
	public class Circle implements Shape {
	 
	   @Override
	   public void draw() {
	      System.out.println("Inside Circle::draw() method.");
	   }
	}
	//创建工厂类可以实现所有所需类的工厂
	public class ShapeFactory {
	    
	   //使用 getShape 方法获取形状类型的对象
	   public Shape getShape(String shapeType){
	      if(shapeType == null){
	         return null;
	      }        
	      if(shapeType.equalsIgnoreCase("CIRCLE")){
	         return new Circle();
	      } else if(shapeType.equalsIgnoreCase("RECTANGLE")){
	         return new Rectangle();
	      } else if(shapeType.equalsIgnoreCase("SQUARE")){
	         return new Square();
	      }
	      return null;
	   }
	}
	//测试类
	public class FactoryPatternDemo {
	 
	   public static void main(String[] args) {
	      ShapeFactory shapeFactory = new ShapeFactory();
	 
	      //获取 Circle 的对象，并调用它的 draw 方法
	      Shape shape1 = shapeFactory.getShape("CIRCLE");
	 
	      //调用 Circle 的 draw 方法
	      shape1.draw();
	 
	      //获取 Rectangle 的对象，并调用它的 draw 方法
	      Shape shape2 = shapeFactory.getShape("RECTANGLE");
	 
	      //调用 Rectangle 的 draw 方法
	      shape2.draw();
	 
	      //获取 Square 的对象，并调用它的 draw 方法
	      Shape shape3 = shapeFactory.getShape("SQUARE");
	 
	      //调用 Square 的 draw 方法
	      shape3.draw();
	   }
	}
	//输出结果
	Inside Circle::draw() method.
	Inside Rectangle::draw() method.
	Inside Square::draw() method.
	`

