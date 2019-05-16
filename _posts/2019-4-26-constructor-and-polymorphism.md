---
layout: post
title:  "构造器与多态"
categories: Java
tags: 多态构造器
author: fidemyuan
---

## 构造器不存在多态

构造器是隐式声明的static方法。
static 方法、属性、构造器、private/final方法都不具备多态行为。但是我们需要考虑一种情况：即在构造器中调用多态方法时的情况。

## 构造器中调用多态方法的情况

###复杂层次结构中构造器的调用顺序

遵从的顺序为：
1.调用基类构造器。这个步骤会不断地反复递归下去，首先是构造这种层次结构的根，然后是下一层导出类，等等，直到最低层的导出类； 
2、按声明的顺序调用成员的初始化方法； 
3、调用导出类构造器的主体。

### 构造器内部的多态方法行为

	 `class Glyph {
	      void draw() { System.out.println("Glyph.draw()"); }
	     Glyph() {
	        System.out.println("Glyph() before draw()");
	        draw();
	        System.out.println("Glyph() after draw()");
	      }
	    }   
	
	    class RoundGlyph extends Glyph {
	      private int radius = 1;
	      RoundGlyph(int r) {
	        radius = r;
	        System.out.println("RoundGlyph.RoundGlyph(), radius = " + radius);
	      }
	      void draw() {
	        System.out.println("RoundGlyph.draw(), radius = " + radius);
	      }
	    }   
	
	    public class PolyConstructors {
	      public static void main(String[] args) {
	        new RoundGlyph(5);
	      }
	    }
	
		//output:结果是：
		Glyph() before draw()
		RoundGlyph.draw(), radius =0
		Glyph() after draw()
		RoundGlyph.RoundGlyph(), radius = 5
			
		`

上面的程序存在一个问题：动态绑定（或后期绑定）的方法的调用是在运行时才决定的，因为对象在程序运行之前无从得知它自己到底是基类的对象，还是某个导出类的对象。如果在基类的构造器内部调用某个动态绑定方法，该方法是被导出类覆盖的，那么这便可能产生难以预料的后果，因为该导出类的对象还未被完全构造，但它的方法却被调用了。

 在创建子类（RoundGlyph）对象时会先调用基类（Glyph）的构造器构造基类对象，而在基类的构造器中却调用了被子类覆盖的动态绑定的方法（draw()）并且输出了radius=0。因为此时的radis值并没有进行赋值，而是系统分配给对象的存储空间的初始值——二进制的0（或者引用是：null）。这并不是我们想要的1。

所以在编写构造器中有一条有效的准则：“用尽可能简单的方法使对象进入正常状态；如果可以的话，避免调用其他方法”。在构造器中，唯一能够安全调用的是基类中的final方法（包括private方法），因为这些方法不能被子类覆盖，也就不会出现上述的问题。