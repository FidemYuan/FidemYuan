---
layout: post
title:  "用继承进行设计"
categories: Java
tags: 多态
author: fidemyuan
---

## 纯继承与扩展

is-a 是一个的关系：这是一种纯粹的继承关系，即父类中有多少方法，子类中重写多少方法，不能自己扩展方法。
is--like-a 像是一个的关系：可以重写相应的方法，也可以扩展相应的一些方法，比较灵活。
is-like-a的关系有一个问题，比如List list = new ArrayList(); 那么list这样一个引用只能调用List中有的，ArrayList中有的或没有的方法，不能调用ArrayList中扩展的方法。

这种情况下必须知道确切的类型才能访问ArrayList中所扩充的方法。也就引出了下面的向下转型。

## 向下转型与运行时类型识别

向上转型会丢失具体类型的信息，访问不到导出类特殊的方法。但是向上转型是安全的因为基类接口不会大于导出类的接口，通过基类接口发送的信息保证都能接收到。

向下转型，则不能保证发送接口的信息都能被接受到，因为得看具体的类型。如果执向的是基类类型，而向导出类引用接口发送对应信息，可能实体对象没有这个接口，是子类特有的。
	
	`
	class Useful{
		public void f(){}
		public void g(){}
	}
	class MoreUseful extends Useful{
		public void f(){}
		public void g(){}
		public void u(){}
		public void v(){}
		public void w(){}
	}
	public class RTTI {
		public static void main(String[] args) {
			Useful[] x = {
					new Useful(),
					new MoreUseful()
			};
			x[0].f();
			x[1].g();

			x[1].u();//会报错，向上转型后，不能调用子类特有的方法
			
			//向下转型 获取方法:
			((MoreUseful) x[1]).u();//Downcast/RTTI
			((MoreUseful) x[0]).u();//Exception thrown这种情况会抛一个异常
		}
	}
	`

