---
layout: post
title: 类型安全的容器
categories: JavaSE
tags: 持有对象
author: fidemyuan
---

## java中常见的容器及不安全所在

java中常见的容器有：数组和集合。
数组的长度固定，只能存储一种类型的容器。所以是安全的容器，而集合可以存储任意的数据，是不安全的。因为在集合中存入很多不同类型的对象，那么在取的时候不知道取出来的是什么，两个不相关的类型的对象转化可能就会出错。比如：


	`class Apple{
	    private static long counter;
	    private final long id = counter++;
	    public long id(){
	        return id;
	    }
	}
	
	class Orange{
	
	}
	
	
	public class ApplesAndOrangesWithoutGenerics {
	    @SuppressWarnings("unchecked")
	    public static void main(String[] args) {
	        ArrayList apples = new ArrayList();
	        for(int i = 0; i<3; i++){
	            apples.add(new Apple());
	        }
	
	        apples.add(new Orange());
	
	        for (int i = 0; i<apples.size(); i++)
	            ((Apple)apples.get(i)).id();
	    }
	}`

如上面代码在存入对象时没有明确泛型，只知道默认是object类型。从容器中取出对象时，由于存入了orange对象，所以取出来转化为Apple对象时就会报错。


## 安全的容器（泛型）

为容器定义泛型可解决容器不安全的问题，不是这个类型根本放不进去

	`class Apple{
	    private static long counter;
	    private final long id = counter++;
	    public long id(){
	        return id;
	    }
	}
	
	class Orange{
	
	}
	
	
	public class ApplesAndOrangesWithoutGenerics {
	   
	    public static void main(String[] args) {
	        ArrayList<Apple> apples = new ArrayList<Apple>();
	        for(int i = 0; i<3; i++){
	            apples.add(new Apple());
	        }
	
	        // apples.add(new Orange());
	
	        for (int i = 0; i<apples.size(); i++)
	            apples.get(i).id(); //不用强制转换
	    }
	}`

	
另外在泛型的容器向上转型的原理同样适用
	
	`class GrannySmith extends Apple{}
	class Gala extends Apple{}
	class Fuji extends Apple{}
	class Braeburn extends Apple{}
	
	public class GenericsAndUpcasting {
	    public static void main(String[] args) {
	        ArrayList<Apple> apples = new ArrayList<Apple>();
	        apples.add(new Gala());
	        apples.add(new GrannySmith());
	        apples.add(new Fuji());
	        apples.add(new Braeburn());
	        for (Apple apple : apples) {
	            System.out.println(apple);
	        }
	
	        List<Apple> list1 = new ArrayList<Apple>();
	        List<Apple> list2 = new LinkedList<Apple>();
	    }
	}`

向上转型为Apple类型存入
