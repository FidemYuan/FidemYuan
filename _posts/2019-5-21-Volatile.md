---
layout: post
title: Volatile
categories: JavaSE
tags: Volatile
author: fidemyuan
---

## Volatile是什么

volatile关键词可以说是java虚拟机提供的最轻量级的同步机制

## 原子性

原子性是指操作不能被线程调度机制中断， 除long和double之外的所有基本类型的读或写操作都是原子操作，注意这里说的读写， 仅指如return i, i = 10, 对于像i++这种操作，包含了读，加1，写指令，所以不是原子操作。 对于long和double的读写，在64位JVM上会把它们当作两个32位来操作，所以不具备原子性。

## 可视性

### JMM

java虚拟机有自己的内存模型（Java Memory Model，JMM），JMM可以屏蔽掉各种硬件和操作系统的内存访问差异，以实现让java程序在各种平台下都能达到一致的内存访问效果。

JMM决定一个线程对共享变量的写入何时对另一个线程可见，JMM定义了线程和主内存之间的抽象关系：共享变量存储在主内存(Main Memory)中，每个线程都有一个私有的本地内存（Local Memory），本地内存保存了被该线程使用到的主内存的副本拷贝，线程对变量的所有操作都必须在工作内存中进行，而不能直接读写主内存中的变量。


### 线程的工作内存之间是不可见

	、public class TestVolatile {
	    boolean status = false;
	
	    /**
	     * 状态切换为true
	     */
	    public void changeStatus(){
	        status = true;
	    }
	
	    /**
	     * 若状态为true，则running。
	     */
	    public void run(){
	        if(status){
	            System.out.println("running....");
	        }
	    }
	}、

如上代码中不能保证输出结果：running，因为在多线程中，线程A将其修改为true这个动作发生在线程A的本地内存中，此时还未同步到主内存中去；而线程B缓存了status的初始值false，此时可能没有观测到status的值被修改了，所以就导致了上述的问题。

## 有序性

编译器在不改变运行结果的情况下，编译器按照自己的喜好对指令进行重排，重排是为了提高代码的运行效率。但是在多线程下，指令重排可能就会出问题。

那么对于共享变量的操作一定要按我们的代码逻辑顺序执行，避免多线程情况下由于重排导致的错误。细点说就是，临界区内与临界区外的代码你可以重排序，我管不着，因为你不影响共享变量的值。但是，你不能将临界区内的代码重排到临界区外面，当然临界区外的代码进入临界区其实是没影响的。

所以有序性就是让编译器在happens-before的规则下不进行指令重排，按顺序执行，确保多线程下程序得到预期的效果。

## Volatile解决了内存可见性问题

因为
1.当写一个volatile变量时，JMM会把该线程对应的本地内存中的变量强制刷新到主内存中去；<br>

2.这个写会操作会导致其他线程中的缓存无效。<br>

因此被Volatile修饰的变量被某个线程改变以后，另外一个线程可以立刻得到。

## Volatile不能解决原子性问题

也就是说Volatile不能解决不具备原子性操作的变量的线程安全问题

比如以下代码
	、public class Counter {
	    public static volatile int num = 0;
	    //使用CountDownLatch来等待计算线程执行完
	    static CountDownLatch countDownLatch = new CountDownLatch(30);
	    public static void main(String []args) throws InterruptedException {
	        //开启30个线程进行累加操作
	        for(int i=0;i<30;i++){
	            new Thread(){
	                public void run(){
	                    for(int j=0;j<10000;j++){
	                        num++;//自加操作
	                    }
	                    countDownLatch.countDown();
	                }
	            }.start();
	        }
	        //等待计算线程执行完
	        countDownLatch.await();
	        System.out.println(num);
	    }
	}、

上面代码运行结果可能为：280123而不是预期的300000<br>


问题就出在num++这个操作上，因为num++不是个原子性的操作，而是个复合操作。我们可以简单讲这个操作理解为由这三步组成:<br>

　　1.读取<br>

　　2.加一<br>

　　3.赋值<br>

所以，在多线程环境下，有可能线程A将num读取到本地内存中，此时其他线程可能已经将num增大了很多，线程A依然对过期的num进行自加，重新写到主存中，最终导致了num的结果不合预期，而是小于300000。<br>

##  Volatile禁止指令重排

在Volatile修饰的变量下禁止指令重排，保持该部分代码运行有序性，确保运行结果达到预期。