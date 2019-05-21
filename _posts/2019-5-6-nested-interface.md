---
layout: post
title: 嵌套接口
categories: JavaSE
tags: 接口
author: fidemyuan
---

## 接口嵌套

接口可以嵌套在类或其它接口中。由于Java中interface内是不可以嵌套class的，所以接口的嵌套就共有两种方式：class嵌套interface、interface嵌套interface。

## class嵌套interface
  这时接口可以是public，private和package的。重点在private上，被定义为私有的接口只能在接口所在的类被实现。可以被实现为public的类也可以被实现为private的类。当被实现为public时，只能在被自身所在的类内部使用。只能够实现接口中的方法,在外部不能像正常类那样上传为接口类型。
	`class A {
	    private interface D {
	        void f();
	    }
	    private class DImp implements D {
	        public void f() {}
	    }
	    public class DImp2 implements D {
	        public void f() {}
	    }
	    public D getD() { return new DImp2(); }
	    private D dRef;
	    public void receiveD(D d) {
	        dRef = d;
	        dRef.f();
	    }
	}
	 
	public class NestingInterfaces {
	    public static void main(String[] args) {
	        A a = new A();
	        //The type A.D is not visible
	        //! A.D ad = a.getD();
	        //Cannot convert from A.D to A.DImp2
	        //! A.DImp2 di2 = a.getD();
	        //The type A.D is not visible
	        //! a.getD().f();        
	        A a2 = new A();
	        a2.receiveD(a.getD());
	    }
	}`

## interface嵌套interface

		`
	interface Flower{
	//接口默认是abstract的的
	//public abstract interface Flower{
	    /**
	     * 心脏
	     */
	    interface FlowerHeart{
	        //接口中定义的变量默认是public static final 型，且必须给其初值
	        public static final int age  = 99;
	
	    }
	    //嵌套接口默认是public，下面写法也可以
	    //public interface FlowerHeart{}
	
	    
	    //嵌套接口默认是public，不能是private，下面写法错误
	    //private interface FlowerHeart{}
	
	}`


  由于接口的元素必须是public的，所以被嵌套的接口自动就是public的，而不能定义成private的。在实现这种嵌套时，不必实现被嵌套的接口