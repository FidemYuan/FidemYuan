---
layout: post
title: 接口的域
categories: JavaSE
tags: 接口
author: fidemyuan
---

## 什么叫接口的域

域是翻译过来的英文中叫"field"指的是字段或者说是属性。接口的域就是指在接口中的成员变量的范围。

##接口域的范围

在Java中，接口的默认的域都是static和final的，而方法默认的也是public的。就是说成员变量默认都是static和final的。

可以证明：

	`final的：
	 
	interface t12 {
	    int ONE = 1, TWO = 2;
	}
	 
	public class Test4 {
	    public static void main(String args[]) {
	        // t12.ONE--;说明是final域
	    }
	}
	
	
	static的：
	
	public class InterfaceDemo implements T12 {
	 
	    public static void main(String[] args) {
	        System.out.println(InterfaceDemo.ONE);
	    }
	}
	 
	interface T12 {
	    int ONE = 1, TWO = 2;
	}`

非静态的域必须用实例名才能引用其域，用类名或接口名引用它就会编译错误，只有静态的域才能直接类名或接口名直接引用。t12.ONE 这个引用没有错，已经证明了ONE是静态的，因为t12不是实例名。
