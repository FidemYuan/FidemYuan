---
layout: post
title: Spring框架简介
categories: JavaSE
tags: Spring框架
author: fidemyuan
---

## 什么是Spring框架

Spring 是java中运用非常广泛的一个轻量级的 DI / IoC 和 AOP 容器的开源框架。

**Spring 的优势**<br>

【1】低侵入 / 低耦合 （降低组件之间的耦合度，实现软件各层之间的解耦）<br>
【2】声明式事务管理（基于切面和惯例）<br>
【3】方便集成其他框架（如MyBatis、Hibernate）<br>
【4】降低 Java 开发难度<br>
【5】Spring 框架中包括了 J2EE 三层的每一层的解决方案（一站式）<br>

**Spring 能帮我们做什么**<br>

【1】Spring 能帮我们根据配置文件创建及组装对象之间的依赖关系。<br>
【2】Spring 面向切面编程能帮助我们无耦合的实现日志记录，性能统计，安全控制。<br>
【3】Spring 能非常简单的帮我们管理数据库事务。<br>
【4】Spring 还提供了与第三方数据访问框架（如Hibernate、JPA）无缝集成，而且自己也提供了一套JDBC访问模板来方便数据库访问。<br>
【5】Spring 还提供与第三方Web（如Struts1/2、JSF）框架无缝集成，而且自己也提供了一套Spring MVC框架，来方便web层搭建。<br>
【6】Spring 能方便的与Java EE（如Java Mail、任务调度）整合，与更多技术整合（比如缓存框架）。<br>

## Spring IOC

IOC控制反转：<br>

是什么技术，而是一种设计思想，就是将原本在程序中手动创建对象的控制权，交由Spring框架来管理。<br>

例子：

1.在 Packge【pojo】下新建一个【Source】类：<br>

	`package pojo;
	
	public class Source {  
	    private String fruit;   // 类型
	    private String sugar;   // 糖分描述
	    private String size;    // 大小杯    
	    /* setter and getter */
	}`
2.在 【src】 目录下新建一个 【applicationContext.xml】 文件，通过 xml 文件配置的方式装配我们的 bean<br>

	`<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <bean name="source" class="pojo.Source">
	        <property name="fruit" value="橙子"/>
	        <property name="sugar" value="多糖"/>
	        <property name="size" value="超大杯"/>
	    </bean>
	</beans>`

3.在 Packge【test】下新建一个【TestSpring】类：
	
	`package test;
	
	import org.junit.Test;
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	import pojo.Source;
	
	public class TestSpring {
	
	    @Test
	    public void test(){
	        ApplicationContext context = new ClassPathXmlApplicationContext(
	                new String[]{"applicationContext.xml"}
	        );
	
	        Source source = (Source) context.getBean("source");
	        System.out.println(source.getFruit());
	        System.out.println(source.getSugar());
	        System.out.println(source.getSize());
	    }
	}`

	得到结果：
	橙子
	多糖
	超大杯


运行结果就可以得到在XML中配置的bean对象,也就是说所谓的IOC就是建立一个对象，把这个对象交给Spring容器来管理。再使用对象的时候，传统方式是去new一个对象，而Spring框架从容器中统一拿出来使用，达到解耦合的目的。<br>

## Spring DI

DI:依赖注入<br>
指 Spring 创建对象的过程中，将对象依赖属性（简单值，集合，对象）通过配置设值给该对象<br>

1.创建一个新类JuiceMaker
	
	`package pojo;
	
	public class JuiceMaker {
	
	    // 唯一关联了一个 Source 对象
	    private Source source = null;
	
	    /* setter and getter */
	
	    public String makeJuice(){
	        String juice = "xxx用户点了一杯" + source.getFruit() + source.getSugar() + source.getSize();
	        return juice;
	    }
	}`

2.在 xml 文件中配置 JuiceMaker 对象：在其中注入依赖的source
	
	`<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <bean name="source" class="pojo.Source">
	        <property name="fruit" value="橙子"/>
	        <property name="sugar" value="多糖"/>
	        <property name="size" value="超大杯"/>
	    </bean>
	    <bean name="juickMaker" class="pojo.JuiceMaker">
	        <property name="source" ref="source" />
	    </bean>
	</beans>`

3.测试

	`package test;
	
	import org.junit.Test;
	import org.springframework.context.ApplicationContext;
	import org.springframework.context.support.ClassPathXmlApplicationContext;
	import pojo.JuiceMaker;
	import pojo.Source;
	
	public class TestSpring {
	
	    @Test
	    public void test(){
	        ApplicationContext context = new ClassPathXmlApplicationContext(
	                new String[]{"applicationContext.xml"}
	        );
	
	        Source source = (Source) context.getBean("source");
	        System.out.println(source.getFruit());
	        System.out.println(source.getSugar());
	        System.out.println(source.getSize());
	
	        JuiceMaker juiceMaker = (JuiceMaker) context.getBean("juickMaker");
	        System.out.println(juiceMaker.makeJuice());
	    }
	}`

	结果：
	
	橙子
	多糖
	超大杯
	XXX用户点了一杯橙子多糖超大杯

DI依赖注入:就是将一个对象交给Spring容器管理后，可以在对象中注入所依赖的对象。这样也就可以降低耦合。

## Spring AOP

### 什么叫AOP
AOP： 面向切面编程。Spring非常重要的功能之一，在数据库事务中切面编程被广泛使用。<br>
可以理解为，一个核心业务，在执行过程中，让周边功能切入核心业务功能中，周边功能就相当于一个切面。<br>

在面向切面编程AOP的思想里面，核心业务功能和切面功能分别独立进行开发，然后把切面功能和核心业务功能 "编织" 在一起，这就叫AOP<br>

### AOP的目的

AOP能够将那些与业务无关，却为业务模块所共同调用的逻辑或责任（例如事务处理、日志管理、权限控制等）封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可拓展性和可维护性。<br>

### AOP中的概念

【1】切入点（Pointcut）<br>
在哪些类，哪些方法上切入（where）<br>
【2】通知（Advice）<br>
在方法执行的什么实际（when:方法前/方法后/方法前后）做什么（what:增强的功能）<br>
【3】切面（Aspect）<br>
切面 = 切入点 + 通知，通俗点就是：在什么时机，什么地方，做什么增强！<br>
【4】织入（Weaving）<br>
把切面加入到对象，并创建出代理对象的过程。（由 Spring 来完成）<br>

### 例子

1.创建一个ProductService类用来实现核心功能：

	`package service;
	
	public class ProductService {
	    public void doSomeService(){
	        System.out.println("doSomeService");
	    }
	}`
2.创建一个切面类LoggerAspect，用来实现周边功能<br>

		`package aspect;
	
	import org.aspectj.lang.ProceedingJoinPoint;
	
	public class LoggerAspect {
	    
	    public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
	        System.out.println("start log:" + joinPoint.getSignature().getName());
	        Object object = joinPoint.proceed();
	        System.out.println("end log:" + joinPoint.getSignature().getName());
	        return object;
	    }
	}`

3.在xml中申明核心功能类和周边功能类

	`<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:aop="http://www.springframework.org/schema/aop"
	       xmlns:tx="http://www.springframework.org/schema/tx"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="
	   http://www.springframework.org/schema/beans
	   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
	   http://www.springframework.org/schema/aop
	   http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
	   http://www.springframework.org/schema/tx
	   http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
	   http://www.springframework.org/schema/context
	   http://www.springframework.org/schema/context/spring-context-3.0.xsd">
	
	    <bean name="productService" class="service.ProductService" />
	    <bean id="loggerAspect" class="aspect.LoggerAspect"/>
	
	    <!-- 配置AOP -->
	    <aop:config>
	        <!-- where：在哪些地方（包.类.方法）做增加 -->
	        <aop:pointcut id="loggerCutpoint"
	                      expression="execution(* service.ProductService.*(..)) "/>
	
	        <!-- what:做什么增强 -->
	        <aop:aspect id="logAspect" ref="loggerAspect">
	            <!-- when:在什么时机（方法前/后/前后） -->
	            <aop:around pointcut-ref="loggerCutpoint" method="log"/>
	        </aop:aspect>
	    </aop:config>
	</beans>`
	
	执行结果为：
	Start log:doSomeService
	doSomeService
	end log:doSomeService

从上述代码中可以看到整个AOP的过程，配置AOP就是指定周边功能如何切入核心业务，切入以后做什么增强。



	


