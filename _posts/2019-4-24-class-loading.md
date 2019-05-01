---
layout: post
title:  "初始化及类的加载"
categories: Java
tags: 类加载
author: fidemyuan
---
## 类加载的顺序

1.在java中虚拟机会试图去寻找带main的方法，加载器会去加载带有main方法的类，编译器会发现其基类，并先开始加载基类，如果还有第二个基类会继续加载直到找到根基类object，开始加载根基类。加载根基类的时候就会同时加载静态资源，包括静态属性、静态代码块（静态资源加载顺序根据上下关系定）。只执行一次。

 1.1首先,给静态成员变量分配内存空间,进行默认初始化
  (整型为0,浮点型为0.0,布尔型为false,字符型为'\u0000',引用型为null)

 1.2其次,执行静态成员变量的初始化操作
  --静态成员的初始化,包括两种: 声明时直接初始化和静态代码块
  --执行顺序为:在代码中的出现的顺序(声明的顺序)


2.类加载好以后，以下情况会导致类加载或者类初始化

(1)创建类的实例(new方法)

(2)调用某个类的静态方法

(3)访问某个类或者接口的静态属性

(4)使用反射机制来强制创建某个类或接口对应的java.lang.Class对象。（Class.forName(“Person”)）

(5)初始化某个类的子类

(6)直接使用java.exe命令来运行某个主类

当初始化的时候：
 2.1.首先,对实例变量,进行默认初始化
  (整型为0,浮点型为0.0,布尔型为false,字符型为'\u0000',引用型为null)
  
 2.2.其次,执行实例变量的初始化操作
  --实例变量的初始化,使用前2种初始化方式: 声明时直接初始化和代码块
  --执行顺序为:在代码中的出现的顺序(声明的顺序)

3.执行构造函数


如下代码：

	public class test {                         //1.第一步，准备加载类
	
	    public static void main(String[] args) {
	        new test();             //4.第四步，new一个类，但在new之前要处理匿名代码块        
	    }
	
	    static int num = 4;      //2.第二步，静态变量和静态代码块的加顺序由编写先后决定 
	
	    {
	        num += 3;
	        System.out.println("b") //5.第五步，按照顺序加载匿名代码块，代码块中有打印
	    }
	
	    int a = 5;                             //6.第六步，按照顺序加载变量
	
	    { // 成员变量第三个
	        System.out.println("c");          //7.第七步，按照顺序打印c
	    }
	
	    test() { // 类的构造函数，第四个加载
	        System.out.println("d");      //8.第八步，最后加载构造函数，完成对象的建立
	    }
	
	    static {           // 3.第三步，静态块，然后执行静态代码块，因为有输出，故打印a
	        System.out.println("a");
	    }
	
	    static void run()           // 静态方法，调用的时候才加载// 注意看，e没有加载
	    {
	        System.out.println("e");
	    }
	}

静态方法在调用的时候才加载


## 在继承中初始化顺序需要注意
 1.当类第一次使用时,JVM就会加载该类,如果该类存在父类,那么就先加载父类,这是一个递归过程,直到Object为止.
 在类加载中,首先进行静态成员变量按照默认值进行初始化,
 然后按照在类中声明的顺序执行静态代码块和静态变量的显示初始化.
 这个过程从父类到子类,并且只会执行一次
 
 2.当父类与子类的静态代码初始化完成后,如果创建了类的对象,
 在初始化子类前,会先对其父类的实例变量进行默认初始化,
 然后按照在类中的声明顺序来执行代码块与实例变量的显示初始化,
 最后调用父类的构造函数,这也是一个递归过程,直到Object类为止.
 (这个过程在每次创建对象时,都会执行)

	` public class CodeBlockForJava extends BaseCodeBlock {
	        {
	            System.out.println("这里是子类的普通代码块");
	        }
	        public CodeBlockForJava() {
	            System.out.println("这里是子类的构造方法");
	        }
	        @Override
	        public void msg() {
	              System.out.println("这里是子类的普通方法");
	        }
	
	        public static void msg2() {
	            System.out.println("这里是子类的静态方法");
	        }
	
	        static {
	            System.out.println("这里是子类的静态代码块");
	        }
	
	        public static void main(String[] args) {
	            BaseCodeBlock bcb = new CodeBlockForJava();
	            bcb.msg();
	        }
	        Other o = new Other();
	    }
	
	    class BaseCodeBlock {
	
	        public BaseCodeBlock() {
	            System.out.println("这里是父类的构造方法");
	        }
	
	        public void msg() {
	            System.out.println("这里是父类的普通方法");
	        }
	
	        public static void msg2() {
	            System.out.println("这里是父类的静态方法");
	        }
	
	        static {
	            System.out.println("这里是父类的静态代码块");
	        }
	
	        Other2 o2 = new Other2();
	
	        {
	            System.out.println("这里是父类的普通代码块");
	        }
	    }
	
	    class Other {
	        Other() {
	            System.out.println("初始化子类的属性值");
	        }
	    }
	
	    class Other2 {
	        Other2() {
	            System.out.println("初始化父类的属性值");
	        }
	    }

	   //输出结果：
	    这里是父类的静态代码块
	    这里是子类的静态代码块
	    初始化父类的属性值
	    这里是父类的普通代码块
	    这里是父类的构造方法
	    这里是子类的普通代码块
	    初始化子类的属性值
	    这里是子类的构造方法
	    这里是子类的普通方法
			`

## 两个例子
	

	` public class ClassloadSort1 {
	
	        public static void main(String[] args) {
	            Singleton.getInstance();
	            System.out.println("Singleton value1:" + Singleton.value1);
	            System.out.println("Singleton value2:" + Singleton.value2);
	    
	            Singleton2.getInstance2();
	            System.out.println("Singleton2 value1:" + Singleton2.value1);
	            System.out.println("Singleton2 value2:" + Singleton2.value2);
	        }
	    }
	    
	    class Singleton {
	        static {
	            System.out.println(Singleton.value1 + "\t" + Singleton.value2 + "\t" + Singleton.singleton);
	            //System.out.println(Singleton.value1 + "\t" + Singleton.value2);
	        }
	        private static Singleton singleton = new Singleton();
	        public static int value1 = 5;
	        public static int value2 = 3;
	    
	        private Singleton() {
	            value1++;
	            value2++;
	        }
	
	        public static Singleton getInstance() {
	            return singleton;
	        }
	
	        int count = 10;
	
	        {
	            System.out.println("count = " + count);
	        }
	    }
	    
	    class Singleton2 {
	        static {
	            System.out.println(Singleton2.value1 + "\t" + Singleton2.value2 + "\t" + Singleton2.singleton2);
	        }
	
	        public static int value1 = 5;
	        public static int value2 = 3;
	        private static Singleton2 singleton2 = new Singleton2();
	        private String sign;
	
	        int count = 20;
	        {
	            System.out.println("count = " + count);
	        }
	
	        private Singleton2() {
	            value1++;
	            value2++;
	        }
	
	        public static Singleton2 getInstance2() {
	            return singleton2;
	        }
	    }
	
	//结果：   
	    Singleton value1:5
	    Singleton value2:3
	
	    Singleton2 value1:6
	    Singleton2 value2:4`


第二个题目：

	`public class StaticTest{
	
	    public static void main(String args[]){
	        staticFunction();
	    }
	    static StaticTest st = new StaticTest();
	    static{
	        System.out.println("1");
	    }
	    StaticTest(){
	        System.out.println("3");
	        System.out.println("a="+a+" b="+b);
	    }
	    public static void staticFunction(){
	        System.out.println("4");
	    }
	    {
	        System.out.println("2");
	    }
	    int a=100;
	    static int b=112;
	}

	//最后的答案是：
	2
	3
	a=100 b=0
	1
	4`

如上面代码解析：

1.类的生命周期是：加载->验证->准备->解析->初始化->使用->卸载，只有在准备阶段和初始化阶段才会涉及类变量的初始化和赋值，因此只针对这两个阶段进行分析；

2.类的准备阶段需要做是为类变量分配内存并设置默认值，因此类变量st为null、b为0；（需要注意的是如果类变量是final在加载阶段就已经完成了初始化，可以把b设置为final试试）；

3.类的初始化阶段需要做是执行类构造器（类构造器是编译器收集所有静态语句块和类变量的赋值语句按语句在源码中的顺序合并生成类构造器，对象的构造方法是init，类的构造方法是cinit，可以在堆栈信息中看到），因此先执行第一条静态变量的赋值语句即st = new StaticTest ()，此时会进行对象的初始化，对象的初始化是先初始化成员变量再执行构造方法，因此打印2->设置a为110->执行构造方法(打印3,此时a已经赋值为110，但是b只是设置了默认值0，并未完成赋值动作)，等对象的初始化完成后继续执行之前的类构造器的语句，接下来就详细说下：

首先执行语句： static StaticTest st = new StaticTest();这个语句等价于static{StaticTest st = new StaticTest() },StaticTest st = new StaticTest()，这个会先执行StaticTest类的成员变量和构造代码块，所以会先执行{System.out.println("2");}这个构造代码块，得出结果2，然后执行 int a=100;这个语句。然后执行StaticTest （）{}构造方法，打印出3，a=100 b=0,最后执行static{    System.out.println("1")；这个静态代码块，结果为1
