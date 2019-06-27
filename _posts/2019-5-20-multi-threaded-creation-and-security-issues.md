---
layout: post
title: 多线程的创造及安全问题
categories: JavaSE
tags: 多线程
author: fidemyuan
---

## 进程线程的基本概念

进程：进程指正在运行的程序。确切的来说，当一个程序进入内存运行，即变成一个进程，进程是处于运行过程中的程序，并且具有一定独立功能。

线程：线程是进程中的一个执行单元，负责当前进程中程序的执行，一个进程中至少有一个线程。一个进程中是可以有多个线程的，这个应用程序也可以称之为多线程程序。

多线程：一个程序中有多个线程在同时执行。

## 线程对CPU资源的调度

一个线程要想运行需要对CPU进行调度使用，一般有两种调度：一是所有线程轮流使用CPU，平均分配每个线程占用CPU的时间。再来是抢占式调度：优先级高的线程先使用CPU，优先级相同的线程则CPU随机执行。

## 线程的执行

实际上CPU的一个核在一个时刻只会执行一个线程，在某个在抢占资源模式下，CPU来回在各个线程之间高速切换，速度很快感觉就像是多个线程同时在进行。

## 为什么要用多线程

1.为了更好的利用cpu的资源，如果只有一个线程，则第二个任务必须等到第一个任务结束后才能进行，如果使用多线程则在主线程执行任务的同时可以执行其他任务，而不需要等待；<br>

2.进程之间不能共享数据，线程可以；<br>

3.系统创建进程需要为该进程重新分配系统资源，创建线程代价比较小；<br>

4.Java语言内置了多线程功能支持，简化了java多线程编程。<br>

## 线程安全问题

在单线程中不会出现线程安全问题，而在多线程编程中有可能会出现多个线程同时访问一个资源，比如一个变量、一个对象、一个文件、一个数据库。这个时候可能就会出现最终的结果与实际不相符的问题。比如经典的4个线程卖票的问题，由于每个线程都同时访问的是这同一个数据，就可能导致在卖出一张票的时候，另外一个线程也同时在卖这一张票，这显然是不对的，这就叫线程安全问题。

##  创建线程的三种方法

1.继承Thread类

	、
	public class Demo{
	public static vpid main(String[] args){
	ThreadDemo	td=new ThreadDemo("zhangsan");
	ThreadDemo  tt=new ThreadDemo("lisi");
	td.start();
	tt.start();


			}
}
	class ThreadDemo extends Thread {
	//设置线程名称
	ThreadDemo(String name){
		super(name);
		}
	//重写run方法

	public void run(){
	for(int i=0;i<5;i++)		
		System.out.println(this.getName()+":run"+i); 	 
	 }
		}

	、

2.实现Runnable接口

	、public class RunnableDemo{

	public staic void main(String[] args){
		RunTest rt=new RunTest();
		
		//创建线程对象
		Thread t1 =new Thread(rt);
		Thread t2 =new Thread(rt);
		//开启线程并调用run方法
		t1.start();
		t2.start();
		}
	}
		
	//定义类实现	Runnable接口

	class RunTest implements Runable{
		private int tick=10;
		//覆盖Runnable接口中的run方法，并将线程要运行的代码放在run方法中
		public void run(){
			while(true){
			if(tick>0){
			System.out.println(Thread.currentTread().getName()+"..."+tick--);
				}

			}
		}

		
		}
	
	、
3.通过Callable和Future创建线程：

	、 public class CallableFutrueTest {
	      public static void main(String[] args) {
	          CallableTest ct = new CallableTest();                        //创建对象
	          FutureTask<Integer> ft = new FutureTask<Integer>(ct);        //使用FutureTask包装CallableTest对象
	          for(int i = 0; i < 100; i++){
	              //输出主线程
	              System.out.println(Thread.currentThread().getName() + "主线程的i为：" + i);
	              //当主线程执行第30次之后开启子线程
	              if(i == 30){        
	                 Thread td = new Thread(ft,"子线程");
	                 td.start();
	             }
	         }
	         //获取并输出子线程call()方法的返回值
	         try {
	             System.out.println("子线程的返回值为" + ft.get());
	         } catch (InterruptedException e) {
	             e.printStackTrace();
	         } catch (ExecutionException e) {
	             e.printStackTrace();
	         }
	     }
	 }
	 class CallableTest implements Callable<Integer>{
	     //复写call() 方法，call()方法具有返回值
	     public Integer call() throws Exception {
	         int i = 0;
	         for( ; i<100; i++){
	             System.out.println(Thread.currentThread().getName() + "的变量值为：" + i);
	         }
	         return i;
	     }
	 }、