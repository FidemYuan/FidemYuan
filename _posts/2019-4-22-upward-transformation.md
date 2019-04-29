---
layout: post
title:  "向上转型"
categories: Java
tags: 多态
author: fidemyuan
---

## 什么叫向上转型

所谓向上转型指将导出类对象用基类的引用代替导出类引用，导出类引用向上转型为基类引用。
比如：

`父类  父类引用 = new 子类（）`

## 向上转型，程序调用情况

	`class Car {
	    public void run() {
	        System.out.println("这是父类run()方法");
	    }
	}
	
	public class Benz extends Car {
	    public void run() {
	        System.out.println("这是Benz的run()方法");
	
	    }
	
	    public void price() {
	        System.out.println("Benz:800000$");
	    }
	
	    public static void main(String[] args) {
	        Car car = new Benz();
	        car.run();
	       //car.price();程序报错
	    }
	}
	//结果：
	这是Benz的run()方法

		`
如上程序Car类型的引用car指向的是子类对象Benz,那么实际上调用的还是子类run()方法

另一方面，car这个对象引用虽然指向子类，但是子类由于进行了向上转型，就失去了使用父类中所没有的方法的“权利”，在此处就是不能调用price()这个方法。

## 向上转型的好处

减少重复代码，父类为参数，调有时用子类作为参数，就是利用了向上转型。这样使代码变得简洁。体现了JAVA的抽象编程思想。

	`class Car {
	    public void run() {
	        System.out.println("这是父类run()方法");
	    }
	
	    public void speed() {
	        System.out.println("speed:0");
	    }
	
	}
	
	class BMW extends Car {
	    public void run() {
	        System.out.println("这是BMW的run()方法");
	    }
	
	    public void speed() {
	        System.out.println("speed:80");
	    }
	}
	
	public class Benz extends Car {
	    public void run() {
	        System.out.println("这是Benz的run()方法");
	
	    }
	
	    public void speed() {
	        System.out.println("speed:100");
	    }
	
	    public void price() {
	        System.out.println("Benz:800000$");
	    }
	
	    public static void main(String[] args) {
	        show(new Benz());//向上转型实现
	        show(new BMW());
	    }
	
	    public static void show(Car car) {//父类实例作为参数
	        car.run();
	        car.speed();
	    }
	}`

上面例子中，show方法中的参数列表，用Car类型接收。在main方法调用时传入Car的子类对象引用时会自动向上转型，在执行程序的时候程序会根据具体的子类类型，执行其特殊的方法。这样本来需要两个show（）方法分别来接受BMW类，Benz类，此时只需要写一个，代码跟简洁，复用性高。

## 向上转型需要注意的地方

在创建子类的时候，会在子类对象中创建一个父类对象。子类中是嵌入了一个父类对象的，私有属性也存在其中，但是子类不能访问，也就是拥有但不能访问。覆盖重写父类方法时，内存中会覆盖父类的对应方法。除了父类部分其他的部分就是子类特有的方法。那么在向上转型，用父类对象引用代替子类对象引用
后，这个引用是只指向子类中父类对象这部分的。所以这个引用不能调用子类特殊方法，调用的是已经被覆盖重写的方法。
