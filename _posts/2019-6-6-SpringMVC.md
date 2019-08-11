---
layout: post
title: Spring MVC
categories: JavaEE
tags: Spring MVC
author: fidemyuan
---

## 什么是Spring MVC

Spring MVC是Spring框架的一个模块,是基于mvc的webframework模块。

## 什么叫MVC

MVC是一种设计模式：model-view-controller。属于web开放模式的一种,以Servlet为主体展开的，由Servlet接收所有的客户端请求，然后根据请求调用相对应的JavaBean，并所有的显示结果交给JSP完成！<br>

M 代表 模型（Model）<br>
模型是什么呢？ 模型就是数据，就是 dao,bean<br>

V 代表 视图（View）<br>
视图是什么呢？ 就是网页, JSP，用来展示模型中的数据<br>

C 代表 控制器（controller)<br>
控制器是什么？ 控制器的作用就是把不同的数据(Model)，显示在不同的视图(View)上，Servlet 扮演的就是这样的角色。<br>

## 由Spring MVC的工作流程来看实现MVC模式
【1】dispatcherServlet
从请求离开浏览器以后，到达DispatcherServlet，Servlet 可以拦截并处理 HTTP 请求，DispatcherServlet 会拦截所有的请求，并且将这些请求发送给 Spring MVC 控制器。<br>

	`<servlet>
	    <servlet-name>dispatcher</servlet-name>
	    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	    <load-on-startup>1</load-on-startup>
	</servlet>
	<servlet-mapping>
	    <servlet-name>dispatcher</servlet-name>
	    <!-- 拦截所有的请求 -->
	    <url-pattern>/</url-pattern>
	</servlet-mapping>`

【2】处理器映射（HandlerMapping）

DispatcherServlet 会查询一个或多个处理器映射来确定接下来去那个处理器，其中处理器映射会根据请求所携带的 URL 信息来进行决策。<br>

处理器映射是需要我们来进行配置

	`<bean id="simpleUrlHandlerMapping"
	      class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
	    <property name="mappings">
	        <props>
	            <!-- /hello 路径的请求交给 id 为 helloController 的控制器处理-->
	            <prop key="/hello">helloController</prop>
	        </props>
	    </property>
	</bean>
	<bean id="helloController" class="controller.HelloController"></bean>`

【3】控制器<br>
 DispatcherServlet通知配置器映射决定了接下来把消息发送至那个控制器，控制器会开始处理请求。<br>
请求处理方式，怎么样返回，是需要程序员根据业务逻辑来编写。<br>

控制器所做的最后一件事就是将模型数据打包，并且表示出用于渲染输出的视图名（逻辑视图名）。它接下来会将请求连同模型和视图名发送回 DispatcherServlet。<br>

【4】返回 DispatcherServlet<br>
当控制器在完成逻辑处理后，通常会产生一些信息，这些信息就是需要返回给用户并在浏览器上显示的信息，它们被称为模型（Model）。仅仅返回原始的信息时不够的——这些信息需要以用户友好的方式进行格式化，一般会是 HTML，所以，信息需要发送给一个视图（view），通常会是 JSP。<br>

【5】视图解析器<br>
控制器传递给 DispatcherServlet 的视图名,不是直接的JSP,所以DispatcherServlet还要通过视图解析器来确定是哪个视图。<br>

【6】渲染出视图响应<br>

确定视图后，视图会和数据渲染，得到浏览器上用户看到的人性化的数据画面。<br>
	
	`<%@ page language="java" contentType="text/html; charset=UTF-8"
	         pageEncoding="UTF-8" isELIgnored="false"%>
	
	<h1>${message}</h1>`

## Spring MVC 的使用

###　ｘｍｌ配置

第一步，建立工程项目，导入需要的相关jar包

第二步，修改web.xml，拦截所有的请求，并交由Spring MVC的后台控制器来处理
	
	`<servlet-mapping>
	    <servlet-name>dispatcher</servlet-name>
	    <url-pattern>/</url-pattern>
	</servlet-mapping>`

第三步，修改dispatcher-servlet.xml配置，配置视图修改器。

	`<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	
	    <bean id="simpleUrlHandlerMapping"
	          class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
	        <property name="mappings">
	            <props>
	                <!-- /hello 路径的请求交给 id 为 helloController 的控制器处理-->
	                <prop key="/hello">helloController</prop>
	            </props>
	        </property>
	    </bean>
	    <bean id="helloController" class="controller.HelloController"></bean>
	</beans>`

第四步，编写 HelloController

	`package controller;
	
	import org.springframework.web.servlet.ModelAndView;
	import org.springframework.web.servlet.mvc.Controller;
	
	public class HelloController implements Controller {
	
	    public ModelAndView handleRequest(javax.servlet.http.HttpServletRequest httpServletRequest, javax.servlet.http.HttpServletResponse httpServletResponse) throws Exception {
	        ModelAndView mav = new ModelAndView("index.jsp");
	        mav.addObject("message", "Hello Spring MVC");
	        return mav;
	    }
	}`

第五步，准备 index.jsp
	
	`<%@ page language="java" contentType="text/html; charset=UTF-8"
	    pageEncoding="UTF-8" isELIgnored="false"%>
	 
	<h1>${message}</h1>`

第六步，部署 Tomcat 及相关环境，启动服务器进行访问。

### 基于注解来配置Spring Mvc

1.首先前面通过xml配置的视图解析器就可以直接在Controller中配置即可

	`package controller;
	
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.servlet.ModelAndView;
	
	@Controller
	public class HelloController{
	
	    @RequestMapping("/hello")
	    public ModelAndView handleRequest(javax.servlet.http.HttpServletRequest httpServletRequest, javax.servlet.http.HttpServletResponse httpServletResponse) throws Exception {
	        ModelAndView mav = new ModelAndView("index.jsp");
	        mav.addObject("message", "Hello Spring MVC");
	        return mav;
	    }
	}`

其中<br>
@Controller 注解：<br>
很明显，这个注解是用来声明控制器的，但实际上这个注解对 Spring MVC 本身的影响并不大。（Spring 实战说它仅仅是辅助实现组件扫描，可以用 @Component 注解代替。<br>

@RequestMapping 注解：<br>
很显然，这就表示路径 /hello 会映射到该方法上<br>

2.那么之前的视图器配置就可以注释，改为扫描<br>

	`<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
	
	    <!--<bean id="simpleUrlHandlerMapping"-->
	                                        <!--class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">-->
	    <!--<property name="mappings">-->
	            <!--<props>-->
	                <!--&lt;!&ndash; /hello 路径的请求交给 id 为 helloController 的控制器处理&ndash;&gt;-->
	                <!--<prop key="/hello">helloController</prop>-->
	            <!--</props>-->
	        <!--</property>-->
	    <!--</bean>-->
	    <!--<bean id="helloController" class="controller.HelloController"></bean>-->
	
	    <!-- 扫描controller下的组件 -->
	    <context:component-scan base-package="controller"/>
	</beans>`

3.重启Tomcat服务器，打开浏览器访问即可。

注意：、
@RequestMapping 注解
如果 @RequestMapping 作用在类上，那么就相当于是给该类所有配置的映射地址前加上了一个地址，例如：<br>

	`@Controller
	@RequestMapping("/wmyskxz")
	public class HelloController {
	    @RequestMapping("/hello")
	    public ModelAndView handleRequest(....) throws Exception {
	        ....
	    }
	}`

访问地址： localhost/wmyskxz/hello

## 数据泄露和影响用户体验问题

需求： 有一些页面我们不希望用户用户直接访问到，例如有重要数据的页面，例如有模型数据支撑的页面。<br>

造成的问题：<br>
我们可以在【web】根目录下放置一个【test.jsp】模拟一个重要数据的页面，我们什么都不用做，重新启动服务器，网页中输入  localhost/test.jsp 就能够直接访问到了，这会造成数据泄露...
另外我们可以直接输入 localhost/index.jsp 试试，根据我们上面的程序，这会是一个空白的页面，因为并没有获取到 ${message} 参数就直接访问了，这会影响用户体验<br>

解决方案

 将JSP 文件配置在【WEB-INF】文件夹中的【page】文件夹下，【WEB-INF】是 Java Web 中默认的安全目录，是不允许用户直接访问的（也就是你说你通过 localhost/WEB-INF/ 这样的方式是永远访问不到的）<br>

在 dispatcher-servlet.xml 文件中做配置<br>

	`<bean id="viewResolver"
	      class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	    <property name="prefix" value="/WEB-INF/page/" />
	    <property name="suffix" value=".jsp" />
	</bean>`


## 控制器请求接受数据

### 1.@RequestParam("前台参数名")

@RequestParam 注解细节：<br>

该注解有三个变量：value、required、defaultvalue<br>
value ：指定 name 属性的名称是什么，value 属性都可以默认不写<br>
required ：是否必须要有该参数，可以设置为【true】或者【false】<br>
defaultvalue ：设置默认值<br>

### 2.使用模型传参
要求： 前台参数名字必须和模型中的字段名一样

让我们先来为我们的表单创建一个 User 模型：

	`package pojo;
	
	public class User {
	    
	    String userName;
	    String password;
	
	    /* getter and setter */
	}`

### 中文乱码问题

跟 Servlet 中的一样，该方法只对 POST 方法有效（因为是直接处理的 request）<br>

通过配置 Spring MVC 字符编码过滤器来完成，在 web.xml 中添加：<br>

	`<filter>
	    <filter-name>CharacterEncodingFilter</filter-name>
	    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
	    <init-param>
	        <param-name>encoding</param-name>
	        <!-- 设置编码格式 -->
	        <param-value>utf-8</param-value>
	    </init-param>
	</filter>
	<filter-mapping>
	    <filter-name>CharacterEncodingFilter</filter-name>
	    <url-pattern>/*</url-pattern>
	</filter-mapping>`

## 控制数据回显

例如先创建一个jsp页面

	`<!DOCTYPE html>
	<%@ page language="java" contentType="text/html; charset=UTF-8"
	         pageEncoding="UTF-8" import="java.util.*" isELIgnored="false" %>
	<html>
	<head>
	    <title>Spring MVC 数据回显</title>
	</head>
	<body>
	<h1>回显数据：${message}</h1>
	</body>
	</html>`

### 1.通过Servlet 原生 API 来实现
	
	`@RequestMapping("/value")
	public ModelAndView handleRequest(HttpServletRequest request,
	                                  HttpServletResponse response) {
	    request.setAttribute("message","成功！");
	    return new ModelAndView("test1");
	}`

### 2.使用 Spring MVC 所提供的 ModelAndView 对象

### 3.使用 Model 对象

### 4.使用 @ModelAttribute 注解：

	`@ModelAttribute
	public void model(Model model) {
	    model.addAttribute("message", "注解成功");
	}
	
	@RequestMapping("/value")
	public String handleRequest() {
	    return "test1";
	}`


在访问控制器方法 handleRequest() 时，会首先调用 model() 方法将 message 添加进页面参数中去

## 客户端跳转

服务器端跳转：request.getRequestDispatcher("地址").forward(request, response);

客户端跳转：

	`@RequestMapping("/hello")
	public ModelAndView handleRequest(javax.servlet.http.HttpServletRequest httpServletRequest, javax.servlet.http.HttpServletResponse httpServletResponse) throws Exception {
	    ModelAndView mav = new ModelAndView("index");
	    mav.addObject("message", "Hello Spring MVC");
	    return mav;
	}
	
	@RequestMapping("/jump")
	public ModelAndView jump() {
	    ModelAndView mav = new ModelAndView("redirect:/hello");
	    return mav;
	}`
或者：

	`@RequestMapping("/jump")
	public String jump() {
	    return "redirect: ./hello";
	}`

## 在Spring MVC中如何完成文件上传

首先导入:<br>
 commons-io-1.3.2.jar 和 commons-fileupload-1.2.1.jar 两个包

第一步：配置上传解析器<br>

在 dispatcher-servlet.xml 中新增一句：<br>

	`<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver"/>
	`
开启对长传功能的支持。

第二步：编写JSP

	`<%@ page contentType="text/html;charset=UTF-8" language="java" %>
	<html>
	<head>
	    <title>测试文件上传</title>
	</head>
	<body>
	<form action="/upload" method="post" enctype="multipart/form-data">
	    <input type="file" name="picture">
	    <input type="submit" value="上 传">
	</form>
	</body>
	</html>`

第三步：编写控制器
	
	`package controller;
	
	import org.springframework.stereotype.Controller;
	import org.springframework.web.bind.annotation.RequestMapping;
	import org.springframework.web.bind.annotation.RequestParam;
	import org.springframework.web.multipart.MultipartFile;
	import org.springframework.web.servlet.ModelAndView;
	
	@Controller
	public class UploadController {
	
	    @RequestMapping("/upload")
	    public void upload(@RequestParam("picture") MultipartFile picture) throws Exception {
	        System.out.println(picture.getOriginalFilename());
	    }
	
	    @RequestMapping("/test2")
	    public ModelAndView upload() {
	        return new ModelAndView("upload");
	    }
	
	}`

最后测试即可。