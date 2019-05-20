---
layout: post
title: 适配接口
categories: JavaSE
tags: 接口
author: fidemyuan
---

## 什么叫适配接口

之前分享过接口是一种规范，一种标准。那么适配接口就是把一个类改造为符合接口的标准。

## 适配器模式

是java设计模式中的一种，适配接口就是采用这一设计模式来改造。分为两种，一种是面向类的适配器模式”，第二种是“面向对象的适配器模式”。

适配是一个把某个类改造为某种标准的过程，而适配器指的就是完成这一过程的类。

为什么不直接在“源”中直接添加方法，适配是为了实现某种目的而为一个源类暂时性的加上某种方法，所以不能破坏原类的结构。同时不这么做也符合Java的高内聚，低耦合的原理。

### 面向类的适配器模式

如下代码Adapter类继承了Person类，而在Java这种单继承的语言中也就意味着，他不可能再去继承其他的类了，这样也就是这个适配器只为Person这一个类服务。所以称其为类适配模式。

	 `
	//下面是“源”也就是需要改造的类
	 public class Person {
	      
	      private String name;
	      private String sex;
	      private int age;
	      
	      public void speakJapanese(){
	          System.out.println("I can speak Japanese!");
	     }
	    
	     public void speakEnglish(){
	         System.out.println("I can speak English!");
	    }
	    ...//以下省略成员变量的get和set方法
	 }

	//下面是目标
	 public interface Job {
	     
	     public abstract void speakJapanese();
	     public abstract void speakEnglish();
	     public abstract void speakFrench();
		}
	    
	//适配器

	 public class Adapter extends Person implements Job{
	 
	     public void speakFrench() {
	         
	     }
	     
	 }	 
	
			`

### 面向对象适配器

对象的适配器模式，把“源”作为一个构造参数传入适配器，然后执行接口所要求的方法。这种适配模式可以为多个源进行适配。弥补了类适配模式的不足。

	 `
	//下面是“源”也就是需要改造的类
	 public class Person {
	      
	      private String name;
	      private String sex;
	      private int age;
	      
	      public void speakJapanese(){
	          System.out.println("I can speak Japanese!");
	     }
	    
	     public void speakEnglish(){
	         System.out.println("I can speak English!");
	    }
	    ...//以下省略成员变量的get和set方法
	 }

	//下面是目标
	 public interface Job {
	     
	     public abstract void speakJapanese();
	     public abstract void speakEnglish();
	     public abstract void speakFrench();
		}
	    
	//适配器
	  public class Adapter implements Job {
	  
	      Person person;
	  
	      public Adapter(Person person) {
	          this.person = person;
	      }
	  
	     public void speakEnglish() {
	         person.speakEnglish();
	     }
	
	     public void speakJapanese() {
	         person.speakJapanese();
	     }
	 
	     //new add
	     public void speakFrench() {
	         
	     }
	 
	 } 
		

	`

### 默认适配器

想实现一个接口但又不想实现所有接口方法，只想去实现一部分方法时，就用默认的适配器模式，他的方法是在接口和具体实现类中添加一个抽象类，用抽象类去空实现目标接口的所有方法。而具体的实现类只需要覆盖其需要完成的方法即可。

	` 
	//下面是目标
	public interface Job {
		     
		     public abstract void speakJapanese();
		     public abstract void speakEnglish();
		     public abstract void speakFrench();
			}

    //下面是创建一个抽象类空实现接口
	
	public abstract class JobDefault implements Job{
	  
	      public void speakChinese() {
	          
	      }
	  
	      public void speakEnglish() {
	          
	      }
	 
	     public void speakFrench() {
	         
	     }
	 
	     public void speakJapanese() {
	         
	     }
	 
	 }
	
	//然后再将需要改造的类继承抽象类，需要实现重写接口的那些方法就去重写抽象类对应的方法

		public class JobImpl extends JobDefault{
		     
		     public void speakChinese(){
		         System.out.println("I can speak Chinese!");
		    }
		     
		 }

		
		`