---
layout: post
title:  "枚举类型"
categories: Java
tags: enum
author: fidemyuan
---

## 什么是枚举类型

是一种特殊的数据类型，类似于类（class）类型一般的存在但是有其特殊的地方存在,关键词enum。 能够为一个变量定义一组预定义的常量。变量必须等于为其预定义的值之一。

不胜枚举中的枚举，就是能够有限的罗列出来。

## 怎么创建

类似于创建一个类类型，如：

	`public enum  RainBow{
		RED, ORANGE, YELLOW, GREEN, CYAN, BLUE, PURPLE	

	}`

	
这样RainBow就是一个枚举类型，有7个具体值，根据需要可以是定义中的其中一种。

## enum类型的方法

	`public enum  RainBow{
		RED, ORANGE, YELLOW, GREEN, CYAN, BLUE, PURPLE	

	}

	public  class test{

		public void main(String [] args){

			RainBow  rainbow = RainBow.RED;
			System.out.println(rainbow)//创建enum时编译器会自动创建toString方法
			
		for(RainBow rb: RainBoe.values())
		System.out.println(rb+"ordinal"+rb.ordinal)//values：产生枚举类型的数组
		//oradinal() 返回对应的索引
				}
			
		}

	结果：
	 	RED

		RED  ordinal  0
		ORANGE ordinal 1
		YELLOW ordinal 2
		GREEN ordinal 3
		CYAN  ordinal 4
		BLUE  ordinal 5
		PURPLE ordinal 6

   `

##  与Switch的搭配

Switch要在有限的可能集合中进行选择，与enum类型绝配。

	`enum Color {GREEN,RED,BLUE}

	public class EnumDemo4 {

    public static void printName(Color color){
        switch (color){
            case BLUE: //无需使用Color进行引用
                System.out.println("蓝色");
                break;
            case RED:
                System.out.println("红色");
                break;
            case GREEN:
                System.out.println("绿色");
                break;
        }
    }

    public static void main(String[] args){
        printName(Color.BLUE);
        printName(Color.RED);
        printName(Color.GREEN);

        //蓝色
        //红色
        //绿色
    }
	}`


