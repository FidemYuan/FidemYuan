---
layout: post
title: 创建内部类
categories: JavaSE
tags: 内部类
author: fidemyuan
---

## 什么叫内部类

定义在一个类内部的类就叫内部类

## 内部类

分为四种：成员内部类、局部内部类、匿名内部类和静态内部类。

### 成员内部类

　1.成员内部类

	`class Circle {
    private double radius = 0;
 
    public Circle(double radius) {
        this.radius = radius;
        getDrawInstance().drawSahpe();   //必须先创建成员内部类的对象，再进行访问
    }
     
    private Draw getDrawInstance() {
        return new Draw();
    }
     
    class Draw {     //内部类
        public void drawSahpe() {
            System.out.println(radius);  //外部类的private成员
			System.out.println(count);   //外部类的静态成员
        }
      }
	}`

如上
1.成员内部类可以无条件访问外部类的所有成员属性和成员方法（包括private成员和静态成员）。
当成员内部类拥有和外部类同名的成员变量或者方法时，会发生隐藏现象，即默认情况下访问的是成员内部类的成员。如果要访问外部类的同名成员，需要以下面的形式进行访问：

外部类.this.成员变量

外部类.this.成员方法

2.在外部类中如果要访问成员内部类的成员，必须先创建一个成员内部类的对象，再通过指向这个对象的引用来访问：

3.要使用内部类时需要先创建外部类
	`public class Test {
	    public static void main(String[] args)  {
	        //第一种方式：
	        Outter outter = new Outter();
	        Outter.Inner inner = outter.new Inner();  //必须通过Outter对象来创建
	         
	        //第二种方式：
	        Outter.Inner inner1 = outter.getInnerInstance();
	    }
	}
	 
	class Outter {
	    private Inner inner = null;
	    public Outter() {
	         
	    }
	     
	    public Inner getInnerInstance() {
	        if(inner == null)
	            inner = new Inner();
	        return inner;
	    }
	      
	    class Inner {
	        public Inner() {
	             
	        }
	    }
	}` 

### 局部内部类

局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内。


	`class People{
	    public People() {
	         
	    }
	}
	 
	class Man{
	    public Man(){
	         
	    }
	     
	    public People getWoman(){
	        class Woman extends People{   //局部内部类
	            int age =0;
	        }
	        return new Woman();
	    }
	}`

局部内部类就像是方法里面的一个局部变量一样，是不能有public、protected、private以及static修饰符的。