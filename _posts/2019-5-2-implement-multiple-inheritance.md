---
layout: post
title: java中如何实现多重继承的效果
categories: JavaSE
tags: 接口
author: fidemyuan
---

## 多重继承

一个类可以同时从多于一个的父类那里继承行为和特征，注意java中为了数据的安全性，只允许单继承，不允许进行多继承。但是我们仍然可以通过其他方式来达到相同的效果。

## 接口多实现

	`interface CanFight {
	    void fight();
	}
	
	interface CanSwim {
	    void swim();
	}
	
	
	interface CanFly {
	    void fly();
	}
	
	public class ActionCharacter {
	    public void fight(){
	        
	    }
	}
	
	public class Hero extends ActionCharacter implements CanFight,CanFly,CanSwim{
	
	    public void fly() {
	    }
	
	    public void swim() {
	    }
	
	    /**
	     * 对于fight()方法，继承父类的，所以不需要显示声明
	     */
	}`
接口的多实现满足了一个类继承多个接口的特征的行为，类似于多继承。

## 内部类

所谓内部类就是比如在S类中创建F、M等其他类，以实现在S类中使用F、M类的行为特征，也就类似于继承了F、M类，实现了多继承的效果。

	`public class Father {
	    public int strong(){
	        return 9;
	    }
	}
	
	public class Mother {
	    public int kind(){
	        return 8;
	    }
	}
	
	
	public class Son {
	    
	    /**
	     * 内部类继承Father类
	     */
	    class Father_1 extends Father{
	        public int strong(){
	            return super.strong() + 1;
	        }
	    }
	    
	    class Mother_1 extends  Mother{
	        public int kind(){
	            return super.kind() - 2;
	        }
	    }
	    
	    public int getStrong(){
	        return new Father_1().strong();
	    }
	    
	    public int getKind(){
	        return new Mother_1().kind();
	    }
	}
	
	public class Test1 {
	
	    public static void main(String[] args) {
	        Son son = new Son();
	        System.out.println("Son 的Strong：" + son.getStrong());
	        System.out.println("Son 的kind：" + son.getKind());
	    }
	
	}
	//output
	
	Son 的Strong：10
	Son 的kind：6
	`
