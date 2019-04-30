---
layout: post
title:  "final关键字"
categories: Java
tags: 关键字
author: fidemyuan
---

## final关键字

final表示最终的，不可改变的，可以修饰变量、方法、类

##　final修饰变量
final修饰基本类型的数据时表示一旦初始化以后就无法改变。

final修饰的是引用的时候也是表示这个引用不能在变化，即对象引用的指向不能改变会一直指向初始化时指向的对象，因为引用里面实际存放的是地址值。而指向的对象自身是可以修改的。

比如：
	`class Man{
	private final int i=0;
	public Man(){
	i=1;//这里会报错，因为i是不能改变的
	final Object obj=new Object();
	obj=new Object;//报错，obj里面的地址也不能改变
	}
	}`

final修饰一个成员变量（属性），必须要显示初始化。这里有两种初始化方式，一种是在变量声明的时候初始化；第二种方法是在声明变量的时候不赋初值，但是要在这个变量所在的类的所有的构造函数中对这个变量赋初值。

### final修饰的变量跟普通变量的区别

	`public class Test {
	    public static void main(String[] args)  {
	        String a = "hello2"; 
	        final String b = "hello";
	        String d = "hello";
	        String c = b + 2; 
	        String e = d + 2;
	        System.out.println((a == c));
	        System.out.println((a == e));
	    }
	}

	//结果：
	true
	false
		`
当final变量是基本数据类型以及String类型时，如果在**编译期间能知道它的确切值**，则编译器会把它当做编译期常量使用。也就是说在用到该final变量的地方，相当于直接访问的这个常量，不需要在运行时确定。那么上面代码中的c就可以看成是“hello2”的一个常量，这个常量跟引用a比较，a指向的数据也是“hello2”所以为true。
但是d没有被final修饰，所以编译器不会把他看做常量，而是引用。==比较的是两者的地址值，显然a,e地址值不相等。


需要注意当final变量是基本数据类型以及String类型时需要在编译器确定这个确切值，编译器才会把它当做常量使用。
	
	`public class Test {
	    public static void main(String[] args)  {
	        String a = "hello2"; 
	        final String b = getHello();
	        String c = b + 2; 
	        System.out.println((a == c));
	 
	    }
	     
	    public static String getHello() {
	        return "hello";
	    }
	}`
因此上述代码的结果就是false了

### final修饰的引用变量指向的对象可变

	`public class Test { 
	    public static void main(String[] args)  { 
	        final MyClass myClass = new MyClass(); 
	        System.out.println(++myClass.i); 
	  
	    } 
	} 
	  
	class MyClass { 
	    public int i = 0; 
	} `

输出结果为1。上述代码说明引用变量被final修饰之后，虽然不能再指向其他对象，但是它指向的对象的内容是可变的。

### final修饰参数

	`
	public class TestFinal {
	    
	    public static void main(String[] args){
	        TestFinal testFinal = new TestFinal();
	        int i = 0;
	        testFinal.changeValue(i);
	        System.out.println(i);
	        
	    }
	    
	    public void changeValue(final int i){
	        //final参数不可改变
	        //i++;
	        System.out.println(i);
	    }
	}`
changeValue方法中的参数i用final修饰之后，就不能在方法中更改变量i的值了。值得注意的一点，方法changeValue和main方法中的变量i根本就不是一个变量，因为java参数传递采用的是值传递，对于基本类型的变量，相当于直接将变量进行了拷贝。所以即使没有final修饰的情况下，在方法内部改变了变量i的值也不会影响方法外的i。


	`public class TestFinal {
	    
	    public static void main(String[] args){
	        TestFinal testFinal = new TestFinal();
	        StringBuffer buffer = new StringBuffer("hello");
	        testFinal.changeValue(buffer);
	        System.out.println(buffer);
	        
	    }
	    
	    public void changeValue((final)StringBuffer buffer){
	        //buffer重新指向另一个对象
	        buffer = new StringBuffer("hi");
	        buffer.append("world");
	        System.out.println(buffer);
	    }
	}
	//结果
	hiworld
	hello
				`

如上伪代码StringBuffer前的final关键词没有时，在changeValue中让buffer指向了其他对象，并不会影响到main方法中的buffer。因为java采用的是值传递，对于引用变量，传递的是引用的值，也就是说让实参和形参同时指向了同一个对象，因此让形参重新指向另一个对象对实参并没有任何影响。

## final修饰类

  当用final修饰一个类时，表明这个类不能被继承。也就是说，如果一个类你永远不会让他被继承，就可以用final进行修饰。final类中的成员变量可以根据需要设为final，但是要注意final类中的所有成员方法都会被隐式地指定为final方法。

## final修饰方法

final,最终的，不可修改的。
final修饰的方法不能被继承类修改，因为方法一旦被final修饰以后类似于private子类没权限访问修改，也就不能覆盖重写此方法。
