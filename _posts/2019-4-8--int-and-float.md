---
layout: post
title:  "控制执行流程"
categories: Java
tags: java 
author: fidemyuan
---

## 对执行流程的控制
在程序执行时必须控制其世界并作出选择。
包括的关键词有：true/false、if-else、while、do-while、for、foreach、return、break、continue、switch


## true和false

　　所有条件语句都利用条件表达式的真或假来决定执行路径。

　　Java不允许使用数字作为布尔值使用。

　　如果想在布尔测试中使用一个非布尔值，如if(a)，那么首先必须使用一个条件表达式将其转换成布尔值，如if(a!=0)。

## if-else

格式：

if(布尔表达式){

   业务逻辑1;

}else{

   业务逻辑2； 
	
}


	`if(Boolean-expression)  
    statement
	或
	if(Boolean-expression) //Boolean-expression为真，执行statement1，否则执行        statement2
    statement1                 
	else
    statement2

	//当然也可以嵌套
	if(Boolean-expression1)
	　   statement1
	else if(Boolean-expression2)
		statement2
	else
		statement3`
## 迭代

所谓迭代就是指：迭代是重复反馈过程的活动，其目的通常是为了逼近所需目标或结果。在编程语言中一般是指：提供一种方法访问一个容器（container）对象中各个元素，而又不需暴露该对象的内部细节。

### while
格式：

while（布尔表达式）{

　　循环体
}

	`while(Boolean-expression) //Boolean-expression为真，执行statement
       statement`

循环开始时会计算一次括号内布尔表达式的值，为ture则执行下面语句，然后再计算布尔表达式，直到为false为止。

### do-while 

格式：
do{

　　循环体

}while（布尔表达式）;

跟whlie的区别是do-whlie中的语句至少会执行一次，而while一旦条件为false则一次都不执行。

### for
格式：
for（初始化表达式；布尔表达式；步进表达式）{

　　循环体

}

for语句中，在第一次迭代之前会进行初始化，随后进行条件测试，满足条件则执行循环体，执行完后再执行步进表达式。随后每次都会进行条件测试，为true则继续迭代执行循环体，再步进又判断，知道条件判断为false为止。

for中的（初始化表达式）initialization和（步进表达式）step可以是逗号表达式，这也是Java唯一用到逗号操作符的地方。此外，在（步进表达式）initialization部分可以对多个同类型的变量进行定义。

比如
	`
		public class TestFor(){

			public static void main(String[] args){

					for(int i=1,j=i+10;i<5;i++,j=i*2){
				
				System.out.println("i="+i+"j="+j);
							}

		} 

		`

###  Foreach
更加简洁的的for语法用于数组和容器。foreach可以让我们在不必创建int变量来对访问项构成的序列进行计数，它可以自动产生这一项

	` public class TestForeach{
			public class void main(String[] args){
			int [] arr ={1,2,3,4};
			for(int a : arr)//定义一个int类型的a,继而把每个arr数组中的值给a
			Systeam.out.println(a);//就可以打印出arr数组中的每个值了
		}

				
		}

	`
## return

return关键词有两个作用：

　　a.指定一个方法返回什么值；

　　b.导致当前方法退出，并返回那个值。

## break和continue
 break：强制退出循环，不执行循环中剩余的语句。

　continue：停止执行当前的迭代，然后退回循环其实处，开始下一次循环。

## switch

　　根据整数表达式的值，switch语句可以从一系列代码中选出一段去执行，属于多路选择。



	`package com.kongzhiliucheng;

		public class Main {

	    public static void main(String[] args) {
	        
        char in='c';
        switch (in){
            case 'a':
                System.out.println(in+" 1");
                break;
            case 'b':
                System.out.println(in+" 2");
                break;
            case 'c':
                System.out.println(in+" 3");
                break;
           default:
                System.out.println();
    	    }
	    }
	}`

##  类似于goto的功能

Java没有goto，但具有类似goto的功能，因为都使用“标签”机制。
### 格式

　　标签是后面跟有：的标识符类似于 label:
或者写成：continue_label: break_label...都行。

标签起作用的唯一地方就是刚好在迭代之前。

### 作用

在迭代之前设置标签的唯一理由是我们希望在其中嵌套另一个迭代或一个开关。　break和continue关键词通常只中断当前循环，但和标签一起使用，会中断循环，跳转到标签所在位置。

比如：
		`label: //一个标签
		outer-iteration{//外部循环
			inner-iteration{
			...
			continue label:;//会终止内部循环回到标签处，再从外部标签
            ...
            break label:;
			}
				
			}
	

		`
如上图代表表示：continue label会终止内部循环外部循环跳转到标签处，继续迭代过程，从外部循环开始。而break label会终止内部循环外部循环跳转到标签处，不会再迭代，实际上是完全终止了两个迭代。