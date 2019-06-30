---
layout: post
title: ArrayList线程不安全及其解决办法
categories: JavaSE
tags: 多线程
author: fidemyuan
---

## ArrayList的不安全案例

ArrayList是线程不安全的类，例如下面例子：
	
	、public  class TestDemo{
		public static void main(String[]agrs){
			
			final ArrayList<String>  arryList = new ArrayList<String>();
			for(int i=0;i<20;i++){
				new Thread(new Runnable(){
					public void run(){
						arrayList.add((UUID.randomUUID+"").substring(0,8));
						System.out.println(arrayList);						
					}
					}).start();

				}
			}
		
	
	}、


上面的代码20个线程操作一个共享数据：ArrayList集合，某个线程再进行add操作的时候可能被其他线程抢走，导致运行结果就会报线程并发安全问题。

## 怎么让其线程安全
1.可以加synchronizedList/Lock锁<br>
2.或者将ArrayList转化为一个线程安全的集合。<br>
Conllections.synchronizdedList()方法<br>

	、public  class TestDemo{
		public static void main(String[]agrs){
			
	final ArrayList<String>  arryList = new ArrayList<String>();
	List<String> synchronizdedList = Conllections.synchronizdedList(arrayList);
			for(int i=0;i<20;i++){
				new Thread(new Runnable(){
					public void run(){
					synchronizdedList.add((UUID.randomUUID+"").substring(0,8));
						System.out.println(synchronizdedList);						
					}
					})

				}
			}
		
	
	}、


3.或者使用新的线程安全的集合来操作<br>
List<String> arrayList = new Vector<String>();

List<String> arrayList = new CopyWriteArryList<String>();

