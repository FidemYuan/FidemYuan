---
layout: post
title: Synchoronized锁与Lock锁
categories: JavaSE
tags: 多线程
author: fidemyuan
---

##  Synchoronized锁

隐式锁<br>
使用Synchoronized关键词来调用，使用方式如下：<br>

	、//使用方式1
	synchoronized(object){
	    //同步代码
	    object.wait();
	    object.notify();
	    object.notifyAll();
	}
	//使用方式2
	public synchronized test1(){
	    //同步代码
	    wait();
	    notify();
	    notifyAll();
	}
	//使用方式3
	public static synchronized test1(){
	    //同步代码
	}、

wait:让当前线程进入阻塞状态，进入锁的等待队列，并且释放当前的锁<br>
notify:随机让一个等待队列中的线程从阻塞状态变为就绪，但是此时并没有释放锁，需要运行到synchronized代码块结束，或者碰到下一个wait方法释放锁。然后和其他线程竞争这个锁(可能有多个线程变为就绪，或者新起线程)。<br>
notifyAll:激活阻塞队列中的所有线程，但是这些线程还是需要竞争锁，因为全都变成就绪态了，如果当前线程退出同步代码块，释放锁了，其他被激活的线程会继续竞争锁。这与notify有所不同，notify只激活了一个线程，即使锁可用了，那些阻塞的线程没有被notify或者notifyAll还是会一直阻塞下去。<br>

## Lock锁

显式锁<br>
使用Lock的方式<br>

	、public void test(){
	    ReentrantLock lock = new ReentrantLock();
	    Condition condition =reentrantLock.newCondition();
	    lock.lock();
	    try{
	        //同步代码
	        condition.await();
	        condition.signal();
	        condition.signalAll();
	    }finally{
	        lock.unlock();
	    }
	}、

ReentrantLock提供了Condition对象，里面的await，signal和signalAll对应Synchoronizede锁中的wait，notify和notifyAll。<br>

## Synchoronized锁与lock锁的区别

一、相同点：都是可重入锁<br>

二、不同点：<br>
1.synchoronized是关键字，属于jvm层面，lock属于api级别的锁<br>

2.使用方法：<br>
synchoronized不需要手动释放，lock需要finally去释放，可能导致死锁。<br>

3.可中断锁：顾名思义，就是可以相应中断的锁。<br>

在Java中，synchronized就不是可中断锁，而Lock是可中断锁。<br>

如果某一线程A正在执行锁中的代码，另一线程B正在等待获取该锁，可能由于等待时间过长，线程B不想等待了，想先处理其他事情，我们可以让它中断自己或者在别的线程中中断它，这种就是可中断锁。<br>

4.公平锁以请求锁的顺序来获取锁，非公平锁则是无法保证按照请求的顺序执行。synchronized就是非公平锁，它无法保证等待的线程获取锁的顺序。而对于ReentrantLock和ReentrantReadWriteLock，它默认情况下是非公平锁，但是可以设置为公平锁。<br>

5.lock具有精准的等待唤醒机制，synchoronized不具备<br>

	、class eggs
	{
	    //判断是否有鸡蛋
	    private boolean flag=false;
	    //第几个鸡蛋
	    private int sum=0;
	 
	    Lock lock=new ReentrantLock();
	    //用当前一个锁 获取2个监视器 一个监视生产鸡蛋  一个监视出售鸡蛋
	    Condition con1=lock.newCondition();
	    Condition con2=lock.newCondition();
	 
	    public void Eggs()
	    {
	        lock.lock();
	        try {
	            if(flag)
	                try {
	                    //this.wait();
	                    con1.await();
	                } catch (Exception e) {
	                    // TODO: handle exception
	                }
	            sum++;
	            System.out.println(Thread.currentThread().getName()+"生产.."+sum);
	            flag=true;
	            con2.signal();
	        }
	        finally {
	            lock.unlock();
	        }
	    }
	    public void Sale()
	    {
	        try {
	            lock.lock();
	            if(!flag)
	                try {
	                    //this.wait();
	                    con2.await();
	                } catch (Exception e) {
	                    // TODO: handle exception
	                }
	            System.out.println(Thread.currentThread().getName()+"...销售.."+sum);
	            flag=false;
	            con1.signal();
	        }
	        finally {
	            lock.unlock();
	        }
	 
	    }
	 
	}
	 
	 
	class Demo implements Runnable
	{
	    private eggs r;
	    private boolean f;//判断是生鸡蛋操作 还是销售操作
	    public Demo(eggs r,boolean f) {
	        // TODO Auto-generated constructor stub
	        this.r=r;
	        this.f=f;
	    }
	    public void run()
	    {
	        if(f)
	            while(true)
	            {
	                try {
	                    Thread.sleep(100);
	                } catch (Exception e) {
	                    // TODO: handle exception
	                }
	                r.Eggs();
	            }
	        else
	            while(true)
	            {
	                try {
	                    Thread.sleep(100);
	                } catch (Exception e) {
	                    // TODO: handle exception
	                }
	                r.Sale();
	            }
	    }
	 
	}
	public class Main {
	    public static void main(String[] args) {
	        eggs e=new eggs();
	        Demo d1=new Demo(e,true);
	        Demo d2=new Demo(e,false);
	        Thread t1=new Thread(d1);
	        Thread t2=new Thread(d2);
	        t1.start();
	        t2.start();
	    }
	}




