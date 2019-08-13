---
layout: post
title: MyBatis
categories: JavaEE
tags: MyBatis
author: fidemyuan
---

##  MyBatis 简介

MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis，是一个基于Java的持久层框架。<br>

持久层： 可以将业务数据存储到磁盘，具备长期存储能力，只要磁盘不损坏，在断电或者其他情况下，重新开启系统仍然可以读取到这些数据。<br>

优点： 可以使用巨大的磁盘空间存储相当量的数据，并且很廉价<br>
缺点：慢（相对于内存而言）<br>

## 与JDBC的比较

Java程序都是通过JDBC（Java Data Base Connectivity）来访问数据库的。JDBC定义了一系列的接口规范，具体的实现是由各数据库厂商去实现，是一种典型的桥接模式。

MyBatis是一个支持普通SQL查询，存储过程和高级映射的优秀持久层框架。

###  优势

1.MyBatis使用SqlSessionFactoryBuilder来连接完成JDBC需要代码完成的数据库获取和连接，减少了代码的重复。<br>
2.JDBC将SQL语句写到代码里，属于硬编码，非常不易维护，MyBatis可以将SQL代码写入xml中，易于修改和维护。<br>
3.JDBC的resultSet需要用户自己去读取并生成对应的POJO，MyBatis的mapper会自动将执行后的结果映射到对应的Java对象中。<br>

## MyBatis 举例使用

1.首先得搭建环境<br>
	1.1下载MyBatis工程包<br>
	1.2为Idear配置环境<br>
		1.2.1下载MyBatis Plugin<br>
		1.2.2破解<br>

2.准备一个数据库<br>
	`DROP DATABASE IF EXISTS mybatis;
	CREATE DATABASE mybatis DEFAULT CHARACTER SET utf8;
	
	use mybatis;
	CREATE TABLE student(
	  id int(11) NOT NULL AUTO_INCREMENT,
	  studentID int(11) NOT NULL UNIQUE,
	  name varchar(255) NOT NULL,
	  PRIMARY KEY (id)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES(1,1,'张三');
	INSERT INTO student VALUES(2,2,'李四');
	INSERT INTO student VALUES(3,3,'王五');`

3.创建工程然后导入必要的 jar 包：<br>

mybatis-3.4.6.jar
mysql-connector-java-5.1.21-bin.jar

4.【pojo】下新建实体类【Student】，用于映射表 student：

	`package pojo;
	
	public class Student {
	
	    int id;
	    int studentID;
	    String name;
	
	    /* getter and setter */
	}`

5.在【src】目录下创建 MyBaits 的主配置文件 mybatis-config.xml ，其主要作用是提供连接数据库用的驱动，数据名称，编码方式，账号密码等.

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	
	    <!-- 别名 -->
	    <typeAliases>
	        <package name="pojo"/>
	    </typeAliases>
	    <!-- 数据库环境 -->
	    <environments default="development">
	        <environment id="development">
	            <transactionManager type="JDBC"/>
	            <dataSource type="POOLED">
	                <property name="driver" value="com.mysql.jdbc.Driver"/>
	                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=UTF-8"/>
	                <property name="username" value="root"/>
	                <property name="password" value="root"/>
	            </dataSource>
	        </environment>
	    </environments>
	    <!-- 映射文件 -->
	    <mappers>
	        <mapper resource="pojo/Student.xml"/>
	    </mappers>
	
	</configuration>`

6.配置Student.xml,写CRUD的操作、模糊查询的操作

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="pojo">
	    <select id="listStudent" resultType="Student">
	        select * from  student
	    </select>
	
	    <insert id="addStudent" parameterType="Student">
	        insert into student (id, studentID, name) values (#{id},#{studentID},#{name})
	    </insert>
	
	    <delete id="deleteStudent" parameterType="Student">
	        delete from student where id = #{id}
	    </delete>
	
	    <select id="getStudent" parameterType="_int" resultType="Student">
	        select * from student where id= #{id}
	    </select>
	
	    <update id="updateStudent" parameterType="Student">
	        update student set name=#{name} where id=#{id}
	    </update>

		<select id="findStudentByName" parameterMap="java.lang.String" resultType="Student">
    	SELECT * FROM student WHERE name LIKE '%${value}%' 
		</select>

	</mapper>`

7.编写测试类实现上述对数据库的操作
	
	`package test;
	
	import org.apache.ibatis.io.Resources;
	import org.apache.ibatis.session.SqlSession;
	import org.apache.ibatis.session.SqlSessionFactory;
	import org.apache.ibatis.session.SqlSessionFactoryBuilder;
	import pojo.Student;
	
	import java.io.IOException;
	import java.io.InputStream;
	import java.util.List;
	
	public class TestMyBatis {
	
	    public static void main(String[] args) throws IOException {
	        // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	        String resource = "mybatis-config.xml";
	        InputStream inputStream = Resources.getResourceAsStream(resource);
	        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	        // 然后根据 sqlSessionFactory 得到 session
	        SqlSession session = sqlSessionFactory.openSession();
	
	        // 增加学生
	        Student student1 = new Student();
	        student1.setId(4);
	        student1.setStudentID(4);
	        student1.setName("新增加的学生");
	        session.insert("addStudent", student1);
	
	        // 删除学生
	        Student student2 = new Student();
	        student2.setId(1);
	        session.delete("deleteStudent", student2);
	
	        // 获取学生
	        Student student3 = session.selectOne("getStudent", 2);
	
	        // 修改学生
	        student3.setName("修改的学生");
	        session.update("updateStudent", student3);
	
	        // 最后通过 session 的 selectList() 方法调用 sql 语句 listStudent
	        List<Student> listStudent = session.selectList("listStudent");
	        for (Student student : listStudent) {
	            System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	        }
	
	        // 提交修改
	        session.commit();
	        // 关闭 session
	        session.close();
	    
			// 模糊查询
	    List<Student> students = session.selectList("findStudentByName", "李四");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
		}

	}`

关于 parameterType： 就是用来在 SQL 映射文件中指定输入参数类型的，可以指定为基本数据类型（如 int、float 等）、包装数据类型（如 String、Interger 等）以及用户自己编写的 JavaBean 封装类<br>

关于 resultType： 在加载 SQL 配置，并绑定指定输入参数和运行 SQL 之后，会得到数据库返回的响应结果，此时使用 resultType 就是用来指定数据库返回的信息对应的 Java 的数据类型。<br>

关于 “#{}” ： 在传统的 JDBC 的编程中，占位符用 “?” 来表示，然后再加载 SQL 之前按照 “?” 的位置设置参数。而 “#{}” 在 MyBatis 中也代表一种占位符，该符号接受输入参数，在大括号中编写参数名称来接受对应参数。当 “#{}” 接受简单类型时可以用 value 或者其他任意名称来获取。<br>

关于 “${}” ： 在 SQL 配置中，有时候需要拼接 SQL 语句（例如模糊查询时），用 “#{}” 是无法达到目的的。在 MyBatis 中，“${}” 代表一个 “拼接符号” ，可以在原有 SQL 语句上拼接新的符合 
SQL 语法的语句。使用 “${}” 拼接符号拼接 SQL ，会引起 SQL 注入，所以一般不建议使用 “${}”。<br>

MyBatis 使用场景： 通过上面的入门程序，不难看出在进行 MyBatis 开发时，我们的大部分精力都放在了 SQL 映射文件上。<br>

MyBatis 的特点就是以 SQL 语句为核心的不完全的 ORM（关系型映射）框架。与 Hibernate 相比，Hibernate 的学习成本比较高，而 SQL 语句并不需要开发人员完成，只需要调用相关 API 即可。这对于开发效率是一个优势，但是缺点是没办法对 SQL 语句进行优化和修改。而 MyBatis 虽然需要开发人员自己配置 SQL 语句，MyBatis 来实现映射关系，但是这样的项目可以适应经常变化的项目需求。所以使用 MyBatis 的场景是：对 SQL 优化要求比较高，或是项目需求或业务经常变动。<br>


##  基本原理

上述对数据库操作的基本原理：

应用程序找 MyBatis 要数据<br>
MyBatis 从数据库中找来数据<br>
1.通过 mybatis-config.xml 定位哪个数据库<br>
2.通过 Student.xml 执行对应的 sql 语句<br>
3.基于 Student.xml 把返回的数据库封装在 Student 对象中<br>
4.把多个 Student 对象装载一个 Student 集合中<br>
返回一个 Student 集合<br>


## 编写日志输出环境配置文件

在开发过程中，最重要的就是在控制台查看程序输出的日志信息，在这里我们选择使用 log4j 工具来输出：<br>

将【MyBatis】文件夹下【lib】中的 log4j 开头的 jar 包都导入工程并添加依赖。<br>
在【src】下新建一个文件 log4j.properties 资源:<br>

	`# Global logging configuration
	# 在开发环境下日志级别要设置成 DEBUG ，生产环境设为 INFO 或 ERROR
	log4j.rootLogger=DEBUG, stdout
	# Console output...
	log4j.appender.stdout=org.apache.log4j.ConsoleAppender
	log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
	log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n`

1.其中，第一条配置语句 “log4j.rootLogger=DEBUG, stdout” 指的是日志输出级别，一共有 7 个级别（OFF、 FATAL、 ERROR、 WARN、 INFO、 DEBUG、 ALL）。<br>

一般常用的日志输出级别分别为 DEBUG、 INFO、 ERROR 以及 WARN，分别表示 “调试级别”、 “标准信息级别”、 “错误级别”、 “异常级别”。如果需要查看程序运行的详细步骤信息，一般选择 “DEBUG” 级别，因为该级别在程序运行期间，会在控制台才打印出底层的运行信息，以及在程序中使用 Log 对象打印出调试信息。<br>

如果是日常的运行，选择 “INFO” 级别，该级别会在控制台打印出程序运行的主要步骤信息。“ERROR” 和 “WARN” 级别分别代表 “不影响程序运行的错误事件” 和 “潜在的错误情形”。<br>

文件中 “stdout” 这段配置的意思就是将 DEBUG 的日志信息输出到 stdout 参数所指定的输出载体中。<br>

2.第二条配置语句 “log4j.appender.stdout=org.apache.log4j.ConsoleAppender” 的含义是，设置名为 stdout 的输出端载体是哪种类型。<br>

目前输出载体有<br>
ConsoleAppender（控制台）<br>
FileAppender（文件）<br>
DailyRollingFileAppender（每天产生一个日志文件）<br>
RollingFileAppender（文件大小到达指定大小时产生一个新的文件）<br>
WriterAppender（将日志信息以流格式发送到任意指定的地方）<br>
这里要将日志打印到 IDEA 的控制台，所以选择 ConsoleAppender<br>

3.第三条配置语句 “log4j.appender.stdout.layout=org.apache.log4j.PatternLayout” 的含义是，名为 stdout 的输出载体的 layout（即界面布局）是哪种类型。<br>

目前输出端的界面类型分为<br>
HTMLLayout（以 HTML 表格形式布局）<br>
PatternLayout（可以灵活地指定布局模式）<br>
SimpleLayout（包含日志信息的级别和信息字符串）<br>
TTCCLayout（包含日志产生的时间、线程、类别等信息）<br>
这里选择灵活指定其布局类型，即自己去配置布局。<br>

4.第四条配置语句 “log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n” 的含义是，如果 layout 界面布局选择了 PatternLayout 灵活布局类型，要指定的打印信息的具体格式。<br>

格式信息配置元素大致如下：<br>
%m 输出代码中的指定的信息<br>
%p 输出优先级，即 DEBUG、 INFO、 WARN、 ERROR 和 FATAL<br>
%r 输出自应用启动到输出该 log 信息耗费的毫秒数<br>
%c 输出所属的类目，通常就是所在类的全名<br>
%t 输出产生该日志事件的线程名<br>
%n 输出一个回车换行符，Windows 平台为 “rn”，UNIX 平台为 “n”<br>
%d 输出日志时的时间或日期，默认个事为 ISO8601，也可以在其后指定格式，比如 %d{yyy MMM dd HH:mm:ss}，输出类似：2018 年 4 月18 日 10:32:00<br>
%l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数<br>

##  MyBatis高级映射

### 一对一查询

就是一张表的信息包含另一张表，比如一个学生表对应一个学号表，学生表信息里面有对应的简短的编号，而学号表中，有编号对应的学号。

1.首先数据库中建表格

	`use mybatis;
	CREATE TABLE student (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  card_id int(11) NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE card (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  number int(11)  NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1',1);
	INSERT INTO student VALUES (2,'student2',2);
	
	INSERT INTO card VALUES (1,1111);
	INSERT INTO card VALUES (2,2222);`

在日常开发中，总是先确定业务的具体 SQL ，后面再将此 SQL 配置在 Mapper 文件中,所以确定查询的SQL语句为：
	`SELECT
	    student.*,
	    card.*
	FROM
	    student,card
	WHERE student.card_id = card.id AND card.number = #{value}`

#### 使用 resultType 实现

2.创建学生 student 表所对应的 Java 实体类 Student，其中封装的属性信息为响应数据库中的字段
		`package pojo;
	
	public class Student {
	
	    int id;
	    String name;
	    int card_id;
	
	    /* getter and setter */
	}`

3.查询的结果是由 resultType 指定的，也就是只能映射一个确定的 Java 包装类，上面的 Stuent 类只包含了学生的基本信息，并没有包含 Card 的信息，所以我们要创建一个最终映射类，以 Student 类为父类，然后追加 Card 的信息：

	`package pojo;
	
	public class StudentAndCard extends Student {
	    private int number;
	
	    /* getter and setter /*
	}`

4.然后在 Student.xml 映射文件中定义 <select> 类型的查询语句 SQL 配置，将之前设计好的 SQL 语句配置进去，然后指定输出参数属性为 resultType，类型为 StudentAndCard 这个 Java 包装类：

	`<select id="findStudentByCard" parameterType="_int" resultType="Student">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>`

5.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentAndCard student = session.selectOne("findStudentByCard",1111);
	    // 获得其姓名并输出
	    System.out.println(student.getName());
	}`


#### 使用resultMap 实现

 resultMap 可以将数据字段映射到名称不一样的响应实体类属性上，重要的是，可以映射实体类中包裹的其他实体类。

2.创建一个封装了 Card 号码和 Student 实体类的 StudentWithCard 类：
	
	`package pojo;
	
	public class StudentWithCard {
	    
	    Student student;
	    int number;
	    int id;
	
	    /* getter and setter */
	}`

3.配置student.xml
	
	`<select id="findStudentByCard" parameterType="_int"resultMap="StudentInfoMap">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>
	
	<resultMap id="StudentInfoMap" type="pojo.StudentWithCard">
	    <!-- id 标签表示对应的主键
	         column 对应查询结果的列值
	         property 对应封装类中的属性名称
	         -->
	    <id column="id" property="id"/>
	    <result column="number" property="number"/>
	    <!-- association 表示关联的嵌套结果，
	         可以简单理解就是为封装类指定的标签 
	         -->
	    <association property="student" javaType="pojo.Student">
	        <id column="id" property="id"/>
	        <result column="name" property="name"/>
	        <result column="card_id" property="card_id"/>
	    </association>
	</resultMap>`

4.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentWithCard student = session.selectOne("findStudentByCard", 1111);
	    // 获得其姓名并输出
	    System.out.println(student.getStudent().getName());
	}`


### 一对多查询

所谓一对多就是，一张数据表中的字段对应了其他多张数据表信息。比如一个班级信息表中，对应了多个学生信息表。

现在要求查询某个班查询到学生列表：

1.数据库中建立数据模型

	`use mybatis;
	CREATE TABLE student (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE class (
	  class_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) NOT NULL,
	  student_id int(11)  NOT NULL,
	  PRIMARY KEY (class_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1');
	INSERT INTO student VALUES (2,'student2');
	
	INSERT INTO class VALUES (1,'Java课',1);
	INSERT INTO class VALUES (2,'Java课',2);`

确定SQL语句为：

	`SELECT 
	  student.*
	FROM
	  student, class
	WHERE student.student_id = class.student_id AND class.class_id = #{value}`

2.创建对应的实体类

	`public class Student {
	
	    private int id;
	    private String name;
	
	    /* getter and setter */
	}
	
	public class Class {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.在 Package【pojo】下新建一个【class.xml】文件完成配置：
	
	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="class">
	    <resultMap id="Students" type="pojo.Student">
	        <id column="student_id" property="id"/>
	        <result column="name" property="name"/>
	    </resultMap>
	    <select id="listStudentByClassName" parameterType="String" resultMap="Students">
	        SELECT
	          student.*
	        FROM
	          student, class
	        WHERE student.student_id = class.student_id AND class.name= #{value}
	    </select>
	</mapper>`

4.编写测试类，得到结果
	
	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 查询上Java课的全部学生
	    List<Student> students = session.selectList("listStudentByClassName", "Java课");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
	}`

### 多对多查询

比如课程跟学生之间的对应关系，课程对应多个学生，学生对应多个课程

1.在数据库中创建信息
	
	`use mybatis;
	CREATE TABLE students (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  student_name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE courses (
	  course_id int(11) NOT NULL AUTO_INCREMENT,
	  course_name varchar(255) NOT NULL,
	  PRIMARY KEY (course_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE student_select_course(
	  s_id int(11) NOT NULL,
	  c_id int(11) NOT NULL,
	  PRIMARY KEY(s_id,c_id)
	) DEFAULT CHARSET=utf8;
	
	INSERT INTO students VALUES (1,'student1');
	INSERT INTO students VALUES (2,'student2');
	
	INSERT INTO courses VALUES (1,'Java课');
	INSERT INTO courses VALUES (2,'Java Web课');
	
	INSERT INTO student_select_course VALUES(1,1);
	INSERT INTO student_select_course VALUES(1,2);
	INSERT INTO student_select_course VALUES(2,1);
	INSERT INTO student_select_course VALUES(2,2);`

SQL语句可设计为：

	`SELECT
	    s.student_id,s.student_name
	FROM
	    students s,student_select_course ssc,courses c
	WHERE s.student_id = ssc.s_id 
	AND ssc.c_id = c.course_id 
	AND c.course_name = #{value}`

2.编写实体类

	`public class Student {
	
	    private int id;
	    private String name;
		private List<Core> students;
	
	    /* getter and setter */
	}
	
	public class Core {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.配置映射文件

	`<resultMap id="Students" type="pojo.Student">
	    <id property="id" column="student_id"/>
	    <result column="student_name" property="name"/>
	</resultMap>
	
	<select id="findStudentsByCourseName" parameterType="String" resultMap="Students">
	    SELECT
	      s.student_id,s.student_name
	    FROM
	      students s,student_select_course ssc,courses c
	    WHERE s.student_id = ssc.s_id
	    AND ssc.c_id = c.course_id
	    AND c.course_name = #{value}
	</select>`

4.编写测试类

		`@Test
		public void test() throws IOException {
		
		    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
		    String resource = "mybatis-config.xml";
		    InputStream inputStream = Resources.getResourceAsStream(resource);
		    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		    // 然后根据 sqlSessionFactory 得到 session
		    SqlSession session = sqlSessionFactory.openSession();
		
		    // 查询上Java课的全部学生
		    List<Student> students = session.selectList("listStudentByClassName", "Java课");
		    for (Student student : students) {
		        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
		    }
		}`

## 延迟加载

什么是延迟加载？从字面上理解，就是对某一类信息的加载之前需要延迟一会儿。在 MyBatis 中，通常会进行多表联合查询，但是有的时候不会立即用到所有的联合查询结果，这时候就可以采用延迟加载的功能。<br>

功能： 延迟加载可以做到，先从单表查询，需要时再从关联表关联查询，这样就大大提高了数据库的性能，因为查询单表要比关联查询多张表速度快。<br>

实例： 如果查询订单并且关联查询用户信息。如果先查询订单信息即可满足要求，当我们需要查询用户信息时再查询用户信息。把对用户信息的按需去查询就是延迟加载。<br>

关联查询：

	`SELECT 
	    orders.*, user.username 
	FROM orders, user
	WHERE orders.user_id = user.id
	延迟加载相当于：
	SELECT 
	    orders.*,
	    (SELECT username FROM USER WHERE orders.user_id = user.id)
	    username 
	FROM orders`

所以这就比较直观了，也就是说，我把关联查询分两次来做，而不是一次性查出所有的。第一步只查询单表orders，必然会查出orders中的一个user_id字段，然后我再根据这个user_id查user表，也是单表查询。<br>

### Mapper 映射配置编写

`<select id="findOrdersUserLazyLoading" resultMap="OrdersUserLazyLoadingResultMap">
    SELECT * FROM orders
</select>`


## Mapper动态代理

通过MyBatis 开发DAO，通常有两种方法：DAO方法和Mapper动态代理方法。

1 SqlSession的使用范围<br>

通过SqlSessionFactory创建SqlSession，而SqlSessionFactory是通过SqlSessionFactoryBuilder进行创建。 
SqlSession是一个面向用户的接口， sqlSession中定义了数据库操作，如：selectOne(返回单个对象)、selectList（返回单个或多个对象）。默认使用DefaultSqlSession实现类。 
SqlSession是线程不安全的，在SqlSesion实现类中除了有接口中的方法（操作数据库的方法,如：查询、插入、更新、删除等）还有数据域属性。 
SqlSession最佳应用场合在方法体内，定义成局部变量使用。

2 SqlSessionFactoryBuilder

SqlSessionFactoryBuilder用于创建SqlSessionFacoty，SqlSessionFacoty一旦创建完成就不需要SqlSessionFactoryBuilder了，因为SqlSession是通过SqlSessionFactory生产，所以可以将SqlSessionFactoryBuilder当成一个工具类使用，最佳使用范围是方法范围即方法体内局部变量。

3 SqlSessionFactory

SqlSessionFactory是一个接口，接口中定义了openSession的不同重载方法，SqlSessionFactory的最佳使用范围是整个应用运行期间，一旦创建后可以重复使用，通常以单例模式管理SqlSessionFactory。

##

---
layout: post
title: MyBatis
categories: JavaEE
tags: MyBatis
author: fidemyuan
---

##  MyBatis 简介

MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis，是一个基于Java的持久层框架。<br>

持久层： 可以将业务数据存储到磁盘，具备长期存储能力，只要磁盘不损坏，在断电或者其他情况下，重新开启系统仍然可以读取到这些数据。<br>

优点： 可以使用巨大的磁盘空间存储相当量的数据，并且很廉价<br>
缺点：慢（相对于内存而言）<br>

## 与JDBC的比较

Java程序都是通过JDBC（Java Data Base Connectivity）来访问数据库的。JDBC定义了一系列的接口规范，具体的实现是由各数据库厂商去实现，是一种典型的桥接模式。

MyBatis是一个支持普通SQL查询，存储过程和高级映射的优秀持久层框架。

###  优势

1.MyBatis使用SqlSessionFactoryBuilder来连接完成JDBC需要代码完成的数据库获取和连接，减少了代码的重复。<br>
2.JDBC将SQL语句写到代码里，属于硬编码，非常不易维护，MyBatis可以将SQL代码写入xml中，易于修改和维护。<br>
3.JDBC的resultSet需要用户自己去读取并生成对应的POJO，MyBatis的mapper会自动将执行后的结果映射到对应的Java对象中。<br>

## MyBatis 举例使用

1.首先得搭建环境<br>
	1.1下载MyBatis工程包<br>
	1.2为Idear配置环境<br>
		1.2.1下载MyBatis Plugin<br>
		1.2.2破解<br>

2.准备一个数据库<br>
	`DROP DATABASE IF EXISTS mybatis;
	CREATE DATABASE mybatis DEFAULT CHARACTER SET utf8;
	
	use mybatis;
	CREATE TABLE student(
	  id int(11) NOT NULL AUTO_INCREMENT,
	  studentID int(11) NOT NULL UNIQUE,
	  name varchar(255) NOT NULL,
	  PRIMARY KEY (id)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES(1,1,'张三');
	INSERT INTO student VALUES(2,2,'李四');
	INSERT INTO student VALUES(3,3,'王五');`

3.创建工程然后导入必要的 jar 包：<br>

mybatis-3.4.6.jar
mysql-connector-java-5.1.21-bin.jar

4.【pojo】下新建实体类【Student】，用于映射表 student：

	`package pojo;
	
	public class Student {
	
	    int id;
	    int studentID;
	    String name;
	
	    /* getter and setter */
	}`

5.在【src】目录下创建 MyBaits 的主配置文件 mybatis-config.xml ，其主要作用是提供连接数据库用的驱动，数据名称，编码方式，账号密码等.

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	
	    <!-- 别名 -->
	    <typeAliases>
	        <package name="pojo"/>
	    </typeAliases>
	    <!-- 数据库环境 -->
	    <environments default="development">
	        <environment id="development">
	            <transactionManager type="JDBC"/>
	            <dataSource type="POOLED">
	                <property name="driver" value="com.mysql.jdbc.Driver"/>
	                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=UTF-8"/>
	                <property name="username" value="root"/>
	                <property name="password" value="root"/>
	            </dataSource>
	        </environment>
	    </environments>
	    <!-- 映射文件 -->
	    <mappers>
	        <mapper resource="pojo/Student.xml"/>
	    </mappers>
	
	</configuration>`

6.配置Student.xml,写CRUD的操作、模糊查询的操作

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="pojo">
	    <select id="listStudent" resultType="Student">
	        select * from  student
	    </select>
	
	    <insert id="addStudent" parameterType="Student">
	        insert into student (id, studentID, name) values (#{id},#{studentID},#{name})
	    </insert>
	
	    <delete id="deleteStudent" parameterType="Student">
	        delete from student where id = #{id}
	    </delete>
	
	    <select id="getStudent" parameterType="_int" resultType="Student">
	        select * from student where id= #{id}
	    </select>
	
	    <update id="updateStudent" parameterType="Student">
	        update student set name=#{name} where id=#{id}
	    </update>

		<select id="findStudentByName" parameterMap="java.lang.String" resultType="Student">
    	SELECT * FROM student WHERE name LIKE '%${value}%' 
		</select>

	</mapper>`

7.编写测试类实现上述对数据库的操作
	
	`package test;
	
	import org.apache.ibatis.io.Resources;
	import org.apache.ibatis.session.SqlSession;
	import org.apache.ibatis.session.SqlSessionFactory;
	import org.apache.ibatis.session.SqlSessionFactoryBuilder;
	import pojo.Student;
	
	import java.io.IOException;
	import java.io.InputStream;
	import java.util.List;
	
	public class TestMyBatis {
	
	    public static void main(String[] args) throws IOException {
	        // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	        String resource = "mybatis-config.xml";
	        InputStream inputStream = Resources.getResourceAsStream(resource);
	        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	        // 然后根据 sqlSessionFactory 得到 session
	        SqlSession session = sqlSessionFactory.openSession();
	
	        // 增加学生
	        Student student1 = new Student();
	        student1.setId(4);
	        student1.setStudentID(4);
	        student1.setName("新增加的学生");
	        session.insert("addStudent", student1);
	
	        // 删除学生
	        Student student2 = new Student();
	        student2.setId(1);
	        session.delete("deleteStudent", student2);
	
	        // 获取学生
	        Student student3 = session.selectOne("getStudent", 2);
	
	        // 修改学生
	        student3.setName("修改的学生");
	        session.update("updateStudent", student3);
	
	        // 最后通过 session 的 selectList() 方法调用 sql 语句 listStudent
	        List<Student> listStudent = session.selectList("listStudent");
	        for (Student student : listStudent) {
	            System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	        }
	
	        // 提交修改
	        session.commit();
	        // 关闭 session
	        session.close();
	    
			// 模糊查询
	    List<Student> students = session.selectList("findStudentByName", "李四");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
		}

	}`

关于 parameterType： 就是用来在 SQL 映射文件中指定输入参数类型的，可以指定为基本数据类型（如 int、float 等）、包装数据类型（如 String、Interger 等）以及用户自己编写的 JavaBean 封装类<br>

关于 resultType： 在加载 SQL 配置，并绑定指定输入参数和运行 SQL 之后，会得到数据库返回的响应结果，此时使用 resultType 就是用来指定数据库返回的信息对应的 Java 的数据类型。<br>

关于 “#{}” ： 在传统的 JDBC 的编程中，占位符用 “?” 来表示，然后再加载 SQL 之前按照 “?” 的位置设置参数。而 “#{}” 在 MyBatis 中也代表一种占位符，该符号接受输入参数，在大括号中编写参数名称来接受对应参数。当 “#{}” 接受简单类型时可以用 value 或者其他任意名称来获取。<br>

关于 “${}” ： 在 SQL 配置中，有时候需要拼接 SQL 语句（例如模糊查询时），用 “#{}” 是无法达到目的的。在 MyBatis 中，“${}” 代表一个 “拼接符号” ，可以在原有 SQL 语句上拼接新的符合 
SQL 语法的语句。使用 “${}” 拼接符号拼接 SQL ，会引起 SQL 注入，所以一般不建议使用 “${}”。<br>

MyBatis 使用场景： 通过上面的入门程序，不难看出在进行 MyBatis 开发时，我们的大部分精力都放在了 SQL 映射文件上。<br>

MyBatis 的特点就是以 SQL 语句为核心的不完全的 ORM（关系型映射）框架。与 Hibernate 相比，Hibernate 的学习成本比较高，而 SQL 语句并不需要开发人员完成，只需要调用相关 API 即可。这对于开发效率是一个优势，但是缺点是没办法对 SQL 语句进行优化和修改。而 MyBatis 虽然需要开发人员自己配置 SQL 语句，MyBatis 来实现映射关系，但是这样的项目可以适应经常变化的项目需求。所以使用 MyBatis 的场景是：对 SQL 优化要求比较高，或是项目需求或业务经常变动。<br>


##  基本原理

上述对数据库操作的基本原理：

应用程序找 MyBatis 要数据<br>
MyBatis 从数据库中找来数据<br>
1.通过 mybatis-config.xml 定位哪个数据库<br>
2.通过 Student.xml 执行对应的 sql 语句<br>
3.基于 Student.xml 把返回的数据库封装在 Student 对象中<br>
4.把多个 Student 对象装载一个 Student 集合中<br>
返回一个 Student 集合<br>


## 编写日志输出环境配置文件

在开发过程中，最重要的就是在控制台查看程序输出的日志信息，在这里我们选择使用 log4j 工具来输出：<br>

将【MyBatis】文件夹下【lib】中的 log4j 开头的 jar 包都导入工程并添加依赖。<br>
在【src】下新建一个文件 log4j.properties 资源:<br>

	`# Global logging configuration
	# 在开发环境下日志级别要设置成 DEBUG ，生产环境设为 INFO 或 ERROR
	log4j.rootLogger=DEBUG, stdout
	# Console output...
	log4j.appender.stdout=org.apache.log4j.ConsoleAppender
	log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
	log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n`

1.其中，第一条配置语句 “log4j.rootLogger=DEBUG, stdout” 指的是日志输出级别，一共有 7 个级别（OFF、 FATAL、 ERROR、 WARN、 INFO、 DEBUG、 ALL）。<br>

一般常用的日志输出级别分别为 DEBUG、 INFO、 ERROR 以及 WARN，分别表示 “调试级别”、 “标准信息级别”、 “错误级别”、 “异常级别”。如果需要查看程序运行的详细步骤信息，一般选择 “DEBUG” 级别，因为该级别在程序运行期间，会在控制台才打印出底层的运行信息，以及在程序中使用 Log 对象打印出调试信息。<br>

如果是日常的运行，选择 “INFO” 级别，该级别会在控制台打印出程序运行的主要步骤信息。“ERROR” 和 “WARN” 级别分别代表 “不影响程序运行的错误事件” 和 “潜在的错误情形”。<br>

文件中 “stdout” 这段配置的意思就是将 DEBUG 的日志信息输出到 stdout 参数所指定的输出载体中。<br>

2.第二条配置语句 “log4j.appender.stdout=org.apache.log4j.ConsoleAppender” 的含义是，设置名为 stdout 的输出端载体是哪种类型。<br>

目前输出载体有<br>
ConsoleAppender（控制台）<br>
FileAppender（文件）<br>
DailyRollingFileAppender（每天产生一个日志文件）<br>
RollingFileAppender（文件大小到达指定大小时产生一个新的文件）<br>
WriterAppender（将日志信息以流格式发送到任意指定的地方）<br>
这里要将日志打印到 IDEA 的控制台，所以选择 ConsoleAppender<br>

3.第三条配置语句 “log4j.appender.stdout.layout=org.apache.log4j.PatternLayout” 的含义是，名为 stdout 的输出载体的 layout（即界面布局）是哪种类型。<br>

目前输出端的界面类型分为<br>
HTMLLayout（以 HTML 表格形式布局）<br>
PatternLayout（可以灵活地指定布局模式）<br>
SimpleLayout（包含日志信息的级别和信息字符串）<br>
TTCCLayout（包含日志产生的时间、线程、类别等信息）<br>
这里选择灵活指定其布局类型，即自己去配置布局。<br>

4.第四条配置语句 “log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n” 的含义是，如果 layout 界面布局选择了 PatternLayout 灵活布局类型，要指定的打印信息的具体格式。<br>

格式信息配置元素大致如下：<br>
%m 输出代码中的指定的信息<br>
%p 输出优先级，即 DEBUG、 INFO、 WARN、 ERROR 和 FATAL<br>
%r 输出自应用启动到输出该 log 信息耗费的毫秒数<br>
%c 输出所属的类目，通常就是所在类的全名<br>
%t 输出产生该日志事件的线程名<br>
%n 输出一个回车换行符，Windows 平台为 “rn”，UNIX 平台为 “n”<br>
%d 输出日志时的时间或日期，默认个事为 ISO8601，也可以在其后指定格式，比如 %d{yyy MMM dd HH:mm:ss}，输出类似：2018 年 4 月18 日 10:32:00<br>
%l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数<br>

##  MyBatis高级映射

### 一对一查询

就是一张表的信息包含另一张表，比如一个学生表对应一个学号表，学生表信息里面有对应的简短的编号，而学号表中，有编号对应的学号。

1.首先数据库中建表格

	`use mybatis;
	CREATE TABLE student (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  card_id int(11) NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE card (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  number int(11)  NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1',1);
	INSERT INTO student VALUES (2,'student2',2);
	
	INSERT INTO card VALUES (1,1111);
	INSERT INTO card VALUES (2,2222);`

在日常开发中，总是先确定业务的具体 SQL ，后面再将此 SQL 配置在 Mapper 文件中,所以确定查询的SQL语句为：
	`SELECT
	    student.*,
	    card.*
	FROM
	    student,card
	WHERE student.card_id = card.id AND card.number = #{value}`

#### 使用 resultType 实现

2.创建学生 student 表所对应的 Java 实体类 Student，其中封装的属性信息为响应数据库中的字段
		`package pojo;
	
	public class Student {
	
	    int id;
	    String name;
	    int card_id;
	
	    /* getter and setter */
	}`

3.查询的结果是由 resultType 指定的，也就是只能映射一个确定的 Java 包装类，上面的 Stuent 类只包含了学生的基本信息，并没有包含 Card 的信息，所以我们要创建一个最终映射类，以 Student 类为父类，然后追加 Card 的信息：

	`package pojo;
	
	public class StudentAndCard extends Student {
	    private int number;
	
	    /* getter and setter /*
	}`

4.然后在 Student.xml 映射文件中定义 <select> 类型的查询语句 SQL 配置，将之前设计好的 SQL 语句配置进去，然后指定输出参数属性为 resultType，类型为 StudentAndCard 这个 Java 包装类：

	`<select id="findStudentByCard" parameterType="_int" resultType="Student">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>`

5.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentAndCard student = session.selectOne("findStudentByCard",1111);
	    // 获得其姓名并输出
	    System.out.println(student.getName());
	}`


#### 使用resultMap 实现

 resultMap 可以将数据字段映射到名称不一样的响应实体类属性上，重要的是，可以映射实体类中包裹的其他实体类。

2.创建一个封装了 Card 号码和 Student 实体类的 StudentWithCard 类：
	
	`package pojo;
	
	public class StudentWithCard {
	    
	    Student student;
	    int number;
	    int id;
	
	    /* getter and setter */
	}`

3.配置student.xml
	
	`<select id="findStudentByCard" parameterType="_int"resultMap="StudentInfoMap">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>
	
	<resultMap id="StudentInfoMap" type="pojo.StudentWithCard">
	    <!-- id 标签表示对应的主键
	         column 对应查询结果的列值
	         property 对应封装类中的属性名称
	         -->
	    <id column="id" property="id"/>
	    <result column="number" property="number"/>
	    <!-- association 表示关联的嵌套结果，
	         可以简单理解就是为封装类指定的标签 
	         -->
	    <association property="student" javaType="pojo.Student">
	        <id column="id" property="id"/>
	        <result column="name" property="name"/>
	        <result column="card_id" property="card_id"/>
	    </association>
	</resultMap>`

4.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentWithCard student = session.selectOne("findStudentByCard", 1111);
	    // 获得其姓名并输出
	    System.out.println(student.getStudent().getName());
	}`


### 一对多查询

所谓一对多就是，一张数据表中的字段对应了其他多张数据表信息。比如一个班级信息表中，对应了多个学生信息表。

现在要求查询某个班查询到学生列表：

1.数据库中建立数据模型

	`use mybatis;
	CREATE TABLE student (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE class (
	  class_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) NOT NULL,
	  student_id int(11)  NOT NULL,
	  PRIMARY KEY (class_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1');
	INSERT INTO student VALUES (2,'student2');
	
	INSERT INTO class VALUES (1,'Java课',1);
	INSERT INTO class VALUES (2,'Java课',2);`

确定SQL语句为：

	`SELECT 
	  student.*
	FROM
	  student, class
	WHERE student.student_id = class.student_id AND class.class_id = #{value}`

2.创建对应的实体类

	`public class Student {
	
	    private int id;
	    private String name;
	
	    /* getter and setter */
	}
	
	public class Class {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.在 Package【pojo】下新建一个【class.xml】文件完成配置：
	
	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="class">
	    <resultMap id="Students" type="pojo.Student">
	        <id column="student_id" property="id"/>
	        <result column="name" property="name"/>
	    </resultMap>
	    <select id="listStudentByClassName" parameterType="String" resultMap="Students">
	        SELECT
	          student.*
	        FROM
	          student, class
	        WHERE student.student_id = class.student_id AND class.name= #{value}
	    </select>
	</mapper>`

4.编写测试类，得到结果
	
	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 查询上Java课的全部学生
	    List<Student> students = session.selectList("listStudentByClassName", "Java课");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
	}`

### 多对多查询

比如课程跟学生之间的对应关系，课程对应多个学生，学生对应多个课程

1.在数据库中创建信息
	
	`use mybatis;
	CREATE TABLE students (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  student_name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE courses (
	  course_id int(11) NOT NULL AUTO_INCREMENT,
	  course_name varchar(255) NOT NULL,
	  PRIMARY KEY (course_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE student_select_course(
	  s_id int(11) NOT NULL,
	  c_id int(11) NOT NULL,
	  PRIMARY KEY(s_id,c_id)
	) DEFAULT CHARSET=utf8;
	
	INSERT INTO students VALUES (1,'student1');
	INSERT INTO students VALUES (2,'student2');
	
	INSERT INTO courses VALUES (1,'Java课');
	INSERT INTO courses VALUES (2,'Java Web课');
	
	INSERT INTO student_select_course VALUES(1,1);
	INSERT INTO student_select_course VALUES(1,2);
	INSERT INTO student_select_course VALUES(2,1);
	INSERT INTO student_select_course VALUES(2,2);`

SQL语句可设计为：

	`SELECT
	    s.student_id,s.student_name
	FROM
	    students s,student_select_course ssc,courses c
	WHERE s.student_id = ssc.s_id 
	AND ssc.c_id = c.course_id 
	AND c.course_name = #{value}`

2.编写实体类

	`public class Student {
	
	    private int id;
	    private String name;
		private List<Core> students;
	
	    /* getter and setter */
	}
	
	public class Core {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.配置映射文件

	`<resultMap id="Students" type="pojo.Student">
	    <id property="id" column="student_id"/>
	    <result column="student_name" property="name"/>
	</resultMap>
	
	<select id="findStudentsByCourseName" parameterType="String" resultMap="Students">
	    SELECT
	      s.student_id,s.student_name
	    FROM
	      students s,student_select_course ssc,courses c
	    WHERE s.student_id = ssc.s_id
	    AND ssc.c_id = c.course_id
	    AND c.course_name = #{value}
	</select>`

4.编写测试类

		`@Test
		public void test() throws IOException {
		
		    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
		    String resource = "mybatis-config.xml";
		    InputStream inputStream = Resources.getResourceAsStream(resource);
		    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		    // 然后根据 sqlSessionFactory 得到 session
		    SqlSession session = sqlSessionFactory.openSession();
		
		    // 查询上Java课的全部学生
		    List<Student> students = session.selectList("listStudentByClassName", "Java课");
		    for (Student student : students) {
		        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
		    }
		}`

## 延迟加载

什么是延迟加载？从字面上理解，就是对某一类信息的加载之前需要延迟一会儿。在 MyBatis 中，通常会进行多表联合查询，但是有的时候不会立即用到所有的联合查询结果，这时候就可以采用延迟加载的功能。<br>

功能： 延迟加载可以做到，先从单表查询，需要时再从关联表关联查询，这样就大大提高了数据库的性能，因为查询单表要比关联查询多张表速度快。<br>

实例： 如果查询订单并且关联查询用户信息。如果先查询订单信息即可满足要求，当我们需要查询用户信息时再查询用户信息。把对用户信息的按需去查询就是延迟加载。<br>

关联查询：

	`SELECT 
	    orders.*, user.username 
	FROM orders, user
	WHERE orders.user_id = user.id
	延迟加载相当于：
	SELECT 
	    orders.*,
	    (SELECT username FROM USER WHERE orders.user_id = user.id)
	    username 
	FROM orders`

所以这就比较直观了，也就是说，我把关联查询分两次来做，而不是一次性查出所有的。第一步只查询单表orders，必然会查出orders中的一个user_id字段，然后我再根据这个user_id查user表，也是单表查询。<br>

### Mapper 映射配置编写

`<select id="findOrdersUserLazyLoading" resultMap="OrdersUserLazyLoadingResultMap">
    SELECT * FROM orders
</select>`


## Mapper动态代理

通过MyBatis 开发DAO，通常有两种方法：DAO方法和Mapper动态代理方法。

1 SqlSession的使用范围<br>

通过SqlSessionFactory创建SqlSession，而SqlSessionFactory是通过SqlSessionFactoryBuilder进行创建。 
SqlSession是一个面向用户的接口， sqlSession中定义了数据库操作，如：selectOne(返回单个对象)、selectList（返回单个或多个对象）。默认使用DefaultSqlSession实现类。 
SqlSession是线程不安全的，在SqlSesion实现类中除了有接口中的方法（操作数据库的方法,如：查询、插入、更新、删除等）还有数据域属性。 
SqlSession最佳应用场合在方法体内，定义成局部变量使用。

2 SqlSessionFactoryBuilder

SqlSessionFactoryBuilder用于创建SqlSessionFacoty，SqlSessionFacoty一旦创建完成就不需要SqlSessionFactoryBuilder了，因为SqlSession是通过SqlSessionFactory生产，所以可以将SqlSessionFactoryBuilder当成一个工具类使用，最佳使用范围是方法范围即方法体内局部变量。

3 SqlSessionFactory

SqlSessionFactory是一个接口，接口中定义了openSession的不同重载方法，SqlSessionFactory的最佳使用范围是整个应用运行期间，一旦创建后可以重复使用，通常以单例模式管理SqlSessionFactory。

##

---
layout: post
title: MyBatis
categories: JavaEE
tags: MyBatis
author: fidemyuan
---

##  MyBatis 简介

MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis，是一个基于Java的持久层框架。<br>

持久层： 可以将业务数据存储到磁盘，具备长期存储能力，只要磁盘不损坏，在断电或者其他情况下，重新开启系统仍然可以读取到这些数据。<br>

优点： 可以使用巨大的磁盘空间存储相当量的数据，并且很廉价<br>
缺点：慢（相对于内存而言）<br>

## 与JDBC的比较

Java程序都是通过JDBC（Java Data Base Connectivity）来访问数据库的。JDBC定义了一系列的接口规范，具体的实现是由各数据库厂商去实现，是一种典型的桥接模式。

MyBatis是一个支持普通SQL查询，存储过程和高级映射的优秀持久层框架。

###  优势

1.MyBatis使用SqlSessionFactoryBuilder来连接完成JDBC需要代码完成的数据库获取和连接，减少了代码的重复。<br>
2.JDBC将SQL语句写到代码里，属于硬编码，非常不易维护，MyBatis可以将SQL代码写入xml中，易于修改和维护。<br>
3.JDBC的resultSet需要用户自己去读取并生成对应的POJO，MyBatis的mapper会自动将执行后的结果映射到对应的Java对象中。<br>

## MyBatis 举例使用

1.首先得搭建环境<br>
	1.1下载MyBatis工程包<br>
	1.2为Idear配置环境<br>
		1.2.1下载MyBatis Plugin<br>
		1.2.2破解<br>

2.准备一个数据库<br>
	`DROP DATABASE IF EXISTS mybatis;
	CREATE DATABASE mybatis DEFAULT CHARACTER SET utf8;
	
	use mybatis;
	CREATE TABLE student(
	  id int(11) NOT NULL AUTO_INCREMENT,
	  studentID int(11) NOT NULL UNIQUE,
	  name varchar(255) NOT NULL,
	  PRIMARY KEY (id)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES(1,1,'张三');
	INSERT INTO student VALUES(2,2,'李四');
	INSERT INTO student VALUES(3,3,'王五');`

3.创建工程然后导入必要的 jar 包：<br>

mybatis-3.4.6.jar
mysql-connector-java-5.1.21-bin.jar

4.【pojo】下新建实体类【Student】，用于映射表 student：

	`package pojo;
	
	public class Student {
	
	    int id;
	    int studentID;
	    String name;
	
	    /* getter and setter */
	}`

5.在【src】目录下创建 MyBaits 的主配置文件 mybatis-config.xml ，其主要作用是提供连接数据库用的驱动，数据名称，编码方式，账号密码等.

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	
	    <!-- 别名 -->
	    <typeAliases>
	        <package name="pojo"/>
	    </typeAliases>
	    <!-- 数据库环境 -->
	    <environments default="development">
	        <environment id="development">
	            <transactionManager type="JDBC"/>
	            <dataSource type="POOLED">
	                <property name="driver" value="com.mysql.jdbc.Driver"/>
	                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=UTF-8"/>
	                <property name="username" value="root"/>
	                <property name="password" value="root"/>
	            </dataSource>
	        </environment>
	    </environments>
	    <!-- 映射文件 -->
	    <mappers>
	        <mapper resource="pojo/Student.xml"/>
	    </mappers>
	
	</configuration>`

6.配置Student.xml,写CRUD的操作、模糊查询的操作

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="pojo">
	    <select id="listStudent" resultType="Student">
	        select * from  student
	    </select>
	
	    <insert id="addStudent" parameterType="Student">
	        insert into student (id, studentID, name) values (#{id},#{studentID},#{name})
	    </insert>
	
	    <delete id="deleteStudent" parameterType="Student">
	        delete from student where id = #{id}
	    </delete>
	
	    <select id="getStudent" parameterType="_int" resultType="Student">
	        select * from student where id= #{id}
	    </select>
	
	    <update id="updateStudent" parameterType="Student">
	        update student set name=#{name} where id=#{id}
	    </update>

		<select id="findStudentByName" parameterMap="java.lang.String" resultType="Student">
    	SELECT * FROM student WHERE name LIKE '%${value}%' 
		</select>

	</mapper>`

7.编写测试类实现上述对数据库的操作
	
	`package test;
	
	import org.apache.ibatis.io.Resources;
	import org.apache.ibatis.session.SqlSession;
	import org.apache.ibatis.session.SqlSessionFactory;
	import org.apache.ibatis.session.SqlSessionFactoryBuilder;
	import pojo.Student;
	
	import java.io.IOException;
	import java.io.InputStream;
	import java.util.List;
	
	public class TestMyBatis {
	
	    public static void main(String[] args) throws IOException {
	        // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	        String resource = "mybatis-config.xml";
	        InputStream inputStream = Resources.getResourceAsStream(resource);
	        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	        // 然后根据 sqlSessionFactory 得到 session
	        SqlSession session = sqlSessionFactory.openSession();
	
	        // 增加学生
	        Student student1 = new Student();
	        student1.setId(4);
	        student1.setStudentID(4);
	        student1.setName("新增加的学生");
	        session.insert("addStudent", student1);
	
	        // 删除学生
	        Student student2 = new Student();
	        student2.setId(1);
	        session.delete("deleteStudent", student2);
	
	        // 获取学生
	        Student student3 = session.selectOne("getStudent", 2);
	
	        // 修改学生
	        student3.setName("修改的学生");
	        session.update("updateStudent", student3);
	
	        // 最后通过 session 的 selectList() 方法调用 sql 语句 listStudent
	        List<Student> listStudent = session.selectList("listStudent");
	        for (Student student : listStudent) {
	            System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	        }
	
	        // 提交修改
	        session.commit();
	        // 关闭 session
	        session.close();
	    
			// 模糊查询
	    List<Student> students = session.selectList("findStudentByName", "李四");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
		}

	}`

关于 parameterType： 就是用来在 SQL 映射文件中指定输入参数类型的，可以指定为基本数据类型（如 int、float 等）、包装数据类型（如 String、Interger 等）以及用户自己编写的 JavaBean 封装类<br>

关于 resultType： 在加载 SQL 配置，并绑定指定输入参数和运行 SQL 之后，会得到数据库返回的响应结果，此时使用 resultType 就是用来指定数据库返回的信息对应的 Java 的数据类型。<br>

关于 “#{}” ： 在传统的 JDBC 的编程中，占位符用 “?” 来表示，然后再加载 SQL 之前按照 “?” 的位置设置参数。而 “#{}” 在 MyBatis 中也代表一种占位符，该符号接受输入参数，在大括号中编写参数名称来接受对应参数。当 “#{}” 接受简单类型时可以用 value 或者其他任意名称来获取。<br>

关于 “${}” ： 在 SQL 配置中，有时候需要拼接 SQL 语句（例如模糊查询时），用 “#{}” 是无法达到目的的。在 MyBatis 中，“${}” 代表一个 “拼接符号” ，可以在原有 SQL 语句上拼接新的符合 
SQL 语法的语句。使用 “${}” 拼接符号拼接 SQL ，会引起 SQL 注入，所以一般不建议使用 “${}”。<br>

MyBatis 使用场景： 通过上面的入门程序，不难看出在进行 MyBatis 开发时，我们的大部分精力都放在了 SQL 映射文件上。<br>

MyBatis 的特点就是以 SQL 语句为核心的不完全的 ORM（关系型映射）框架。与 Hibernate 相比，Hibernate 的学习成本比较高，而 SQL 语句并不需要开发人员完成，只需要调用相关 API 即可。这对于开发效率是一个优势，但是缺点是没办法对 SQL 语句进行优化和修改。而 MyBatis 虽然需要开发人员自己配置 SQL 语句，MyBatis 来实现映射关系，但是这样的项目可以适应经常变化的项目需求。所以使用 MyBatis 的场景是：对 SQL 优化要求比较高，或是项目需求或业务经常变动。<br>


##  基本原理

上述对数据库操作的基本原理：

应用程序找 MyBatis 要数据<br>
MyBatis 从数据库中找来数据<br>
1.通过 mybatis-config.xml 定位哪个数据库<br>
2.通过 Student.xml 执行对应的 sql 语句<br>
3.基于 Student.xml 把返回的数据库封装在 Student 对象中<br>
4.把多个 Student 对象装载一个 Student 集合中<br>
返回一个 Student 集合<br>


## 编写日志输出环境配置文件

在开发过程中，最重要的就是在控制台查看程序输出的日志信息，在这里我们选择使用 log4j 工具来输出：<br>

将【MyBatis】文件夹下【lib】中的 log4j 开头的 jar 包都导入工程并添加依赖。<br>
在【src】下新建一个文件 log4j.properties 资源:<br>

	`# Global logging configuration
	# 在开发环境下日志级别要设置成 DEBUG ，生产环境设为 INFO 或 ERROR
	log4j.rootLogger=DEBUG, stdout
	# Console output...
	log4j.appender.stdout=org.apache.log4j.ConsoleAppender
	log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
	log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n`

1.其中，第一条配置语句 “log4j.rootLogger=DEBUG, stdout” 指的是日志输出级别，一共有 7 个级别（OFF、 FATAL、 ERROR、 WARN、 INFO、 DEBUG、 ALL）。<br>

一般常用的日志输出级别分别为 DEBUG、 INFO、 ERROR 以及 WARN，分别表示 “调试级别”、 “标准信息级别”、 “错误级别”、 “异常级别”。如果需要查看程序运行的详细步骤信息，一般选择 “DEBUG” 级别，因为该级别在程序运行期间，会在控制台才打印出底层的运行信息，以及在程序中使用 Log 对象打印出调试信息。<br>

如果是日常的运行，选择 “INFO” 级别，该级别会在控制台打印出程序运行的主要步骤信息。“ERROR” 和 “WARN” 级别分别代表 “不影响程序运行的错误事件” 和 “潜在的错误情形”。<br>

文件中 “stdout” 这段配置的意思就是将 DEBUG 的日志信息输出到 stdout 参数所指定的输出载体中。<br>

2.第二条配置语句 “log4j.appender.stdout=org.apache.log4j.ConsoleAppender” 的含义是，设置名为 stdout 的输出端载体是哪种类型。<br>

目前输出载体有<br>
ConsoleAppender（控制台）<br>
FileAppender（文件）<br>
DailyRollingFileAppender（每天产生一个日志文件）<br>
RollingFileAppender（文件大小到达指定大小时产生一个新的文件）<br>
WriterAppender（将日志信息以流格式发送到任意指定的地方）<br>
这里要将日志打印到 IDEA 的控制台，所以选择 ConsoleAppender<br>

3.第三条配置语句 “log4j.appender.stdout.layout=org.apache.log4j.PatternLayout” 的含义是，名为 stdout 的输出载体的 layout（即界面布局）是哪种类型。<br>

目前输出端的界面类型分为<br>
HTMLLayout（以 HTML 表格形式布局）<br>
PatternLayout（可以灵活地指定布局模式）<br>
SimpleLayout（包含日志信息的级别和信息字符串）<br>
TTCCLayout（包含日志产生的时间、线程、类别等信息）<br>
这里选择灵活指定其布局类型，即自己去配置布局。<br>

4.第四条配置语句 “log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n” 的含义是，如果 layout 界面布局选择了 PatternLayout 灵活布局类型，要指定的打印信息的具体格式。<br>

格式信息配置元素大致如下：<br>
%m 输出代码中的指定的信息<br>
%p 输出优先级，即 DEBUG、 INFO、 WARN、 ERROR 和 FATAL<br>
%r 输出自应用启动到输出该 log 信息耗费的毫秒数<br>
%c 输出所属的类目，通常就是所在类的全名<br>
%t 输出产生该日志事件的线程名<br>
%n 输出一个回车换行符，Windows 平台为 “rn”，UNIX 平台为 “n”<br>
%d 输出日志时的时间或日期，默认个事为 ISO8601，也可以在其后指定格式，比如 %d{yyy MMM dd HH:mm:ss}，输出类似：2018 年 4 月18 日 10:32:00<br>
%l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数<br>

##  MyBatis高级映射

### 一对一查询

就是一张表的信息包含另一张表，比如一个学生表对应一个学号表，学生表信息里面有对应的简短的编号，而学号表中，有编号对应的学号。

1.首先数据库中建表格

	`use mybatis;
	CREATE TABLE student (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  card_id int(11) NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE card (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  number int(11)  NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1',1);
	INSERT INTO student VALUES (2,'student2',2);
	
	INSERT INTO card VALUES (1,1111);
	INSERT INTO card VALUES (2,2222);`

在日常开发中，总是先确定业务的具体 SQL ，后面再将此 SQL 配置在 Mapper 文件中,所以确定查询的SQL语句为：
	`SELECT
	    student.*,
	    card.*
	FROM
	    student,card
	WHERE student.card_id = card.id AND card.number = #{value}`

#### 使用 resultType 实现

2.创建学生 student 表所对应的 Java 实体类 Student，其中封装的属性信息为响应数据库中的字段
		`package pojo;
	
	public class Student {
	
	    int id;
	    String name;
	    int card_id;
	
	    /* getter and setter */
	}`

3.查询的结果是由 resultType 指定的，也就是只能映射一个确定的 Java 包装类，上面的 Stuent 类只包含了学生的基本信息，并没有包含 Card 的信息，所以我们要创建一个最终映射类，以 Student 类为父类，然后追加 Card 的信息：

	`package pojo;
	
	public class StudentAndCard extends Student {
	    private int number;
	
	    /* getter and setter /*
	}`

4.然后在 Student.xml 映射文件中定义 <select> 类型的查询语句 SQL 配置，将之前设计好的 SQL 语句配置进去，然后指定输出参数属性为 resultType，类型为 StudentAndCard 这个 Java 包装类：

	`<select id="findStudentByCard" parameterType="_int" resultType="Student">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>`

5.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentAndCard student = session.selectOne("findStudentByCard",1111);
	    // 获得其姓名并输出
	    System.out.println(student.getName());
	}`


#### 使用resultMap 实现

 resultMap 可以将数据字段映射到名称不一样的响应实体类属性上，重要的是，可以映射实体类中包裹的其他实体类。

2.创建一个封装了 Card 号码和 Student 实体类的 StudentWithCard 类：
	
	`package pojo;
	
	public class StudentWithCard {
	    
	    Student student;
	    int number;
	    int id;
	
	    /* getter and setter */
	}`

3.配置student.xml
	
	`<select id="findStudentByCard" parameterType="_int"resultMap="StudentInfoMap">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>
	
	<resultMap id="StudentInfoMap" type="pojo.StudentWithCard">
	    <!-- id 标签表示对应的主键
	         column 对应查询结果的列值
	         property 对应封装类中的属性名称
	         -->
	    <id column="id" property="id"/>
	    <result column="number" property="number"/>
	    <!-- association 表示关联的嵌套结果，
	         可以简单理解就是为封装类指定的标签 
	         -->
	    <association property="student" javaType="pojo.Student">
	        <id column="id" property="id"/>
	        <result column="name" property="name"/>
	        <result column="card_id" property="card_id"/>
	    </association>
	</resultMap>`

4.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentWithCard student = session.selectOne("findStudentByCard", 1111);
	    // 获得其姓名并输出
	    System.out.println(student.getStudent().getName());
	}`


### 一对多查询

所谓一对多就是，一张数据表中的字段对应了其他多张数据表信息。比如一个班级信息表中，对应了多个学生信息表。

现在要求查询某个班查询到学生列表：

1.数据库中建立数据模型

	`use mybatis;
	CREATE TABLE student (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE class (
	  class_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) NOT NULL,
	  student_id int(11)  NOT NULL,
	  PRIMARY KEY (class_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1');
	INSERT INTO student VALUES (2,'student2');
	
	INSERT INTO class VALUES (1,'Java课',1);
	INSERT INTO class VALUES (2,'Java课',2);`

确定SQL语句为：

	`SELECT 
	  student.*
	FROM
	  student, class
	WHERE student.student_id = class.student_id AND class.class_id = #{value}`

2.创建对应的实体类

	`public class Student {
	
	    private int id;
	    private String name;
	
	    /* getter and setter */
	}
	
	public class Class {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.在 Package【pojo】下新建一个【class.xml】文件完成配置：
	
	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="class">
	    <resultMap id="Students" type="pojo.Student">
	        <id column="student_id" property="id"/>
	        <result column="name" property="name"/>
	    </resultMap>
	    <select id="listStudentByClassName" parameterType="String" resultMap="Students">
	        SELECT
	          student.*
	        FROM
	          student, class
	        WHERE student.student_id = class.student_id AND class.name= #{value}
	    </select>
	</mapper>`

4.编写测试类，得到结果
	
	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 查询上Java课的全部学生
	    List<Student> students = session.selectList("listStudentByClassName", "Java课");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
	}`

### 多对多查询

比如课程跟学生之间的对应关系，课程对应多个学生，学生对应多个课程

1.在数据库中创建信息
	
	`use mybatis;
	CREATE TABLE students (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  student_name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE courses (
	  course_id int(11) NOT NULL AUTO_INCREMENT,
	  course_name varchar(255) NOT NULL,
	  PRIMARY KEY (course_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE student_select_course(
	  s_id int(11) NOT NULL,
	  c_id int(11) NOT NULL,
	  PRIMARY KEY(s_id,c_id)
	) DEFAULT CHARSET=utf8;
	
	INSERT INTO students VALUES (1,'student1');
	INSERT INTO students VALUES (2,'student2');
	
	INSERT INTO courses VALUES (1,'Java课');
	INSERT INTO courses VALUES (2,'Java Web课');
	
	INSERT INTO student_select_course VALUES(1,1);
	INSERT INTO student_select_course VALUES(1,2);
	INSERT INTO student_select_course VALUES(2,1);
	INSERT INTO student_select_course VALUES(2,2);`

SQL语句可设计为：

	`SELECT
	    s.student_id,s.student_name
	FROM
	    students s,student_select_course ssc,courses c
	WHERE s.student_id = ssc.s_id 
	AND ssc.c_id = c.course_id 
	AND c.course_name = #{value}`

2.编写实体类

	`public class Student {
	
	    private int id;
	    private String name;
		private List<Core> students;
	
	    /* getter and setter */
	}
	
	public class Core {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.配置映射文件

	`<resultMap id="Students" type="pojo.Student">
	    <id property="id" column="student_id"/>
	    <result column="student_name" property="name"/>
	</resultMap>
	
	<select id="findStudentsByCourseName" parameterType="String" resultMap="Students">
	    SELECT
	      s.student_id,s.student_name
	    FROM
	      students s,student_select_course ssc,courses c
	    WHERE s.student_id = ssc.s_id
	    AND ssc.c_id = c.course_id
	    AND c.course_name = #{value}
	</select>`

4.编写测试类

		`@Test
		public void test() throws IOException {
		
		    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
		    String resource = "mybatis-config.xml";
		    InputStream inputStream = Resources.getResourceAsStream(resource);
		    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		    // 然后根据 sqlSessionFactory 得到 session
		    SqlSession session = sqlSessionFactory.openSession();
		
		    // 查询上Java课的全部学生
		    List<Student> students = session.selectList("listStudentByClassName", "Java课");
		    for (Student student : students) {
		        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
		    }
		}`

## 延迟加载

什么是延迟加载？从字面上理解，就是对某一类信息的加载之前需要延迟一会儿。在 MyBatis 中，通常会进行多表联合查询，但是有的时候不会立即用到所有的联合查询结果，这时候就可以采用延迟加载的功能。<br>

功能： 延迟加载可以做到，先从单表查询，需要时再从关联表关联查询，这样就大大提高了数据库的性能，因为查询单表要比关联查询多张表速度快。<br>

实例： 如果查询订单并且关联查询用户信息。如果先查询订单信息即可满足要求，当我们需要查询用户信息时再查询用户信息。把对用户信息的按需去查询就是延迟加载。<br>

关联查询：

	`SELECT 
	    orders.*, user.username 
	FROM orders, user
	WHERE orders.user_id = user.id
	延迟加载相当于：
	SELECT 
	    orders.*,
	    (SELECT username FROM USER WHERE orders.user_id = user.id)
	    username 
	FROM orders`

所以这就比较直观了，也就是说，我把关联查询分两次来做，而不是一次性查出所有的。第一步只查询单表orders，必然会查出orders中的一个user_id字段，然后我再根据这个user_id查user表，也是单表查询。<br>

### Mapper 映射配置编写

`<select id="findOrdersUserLazyLoading" resultMap="OrdersUserLazyLoadingResultMap">
    SELECT * FROM orders
</select>`


## Mapper动态代理

通过MyBatis 开发DAO，通常有两种方法：DAO方法和Mapper动态代理方法。

1 SqlSession的使用范围<br>

通过SqlSessionFactory创建SqlSession，而SqlSessionFactory是通过SqlSessionFactoryBuilder进行创建。 
SqlSession是一个面向用户的接口， sqlSession中定义了数据库操作，如：selectOne(返回单个对象)、selectList（返回单个或多个对象）。默认使用DefaultSqlSession实现类。 
SqlSession是线程不安全的，在SqlSesion实现类中除了有接口中的方法（操作数据库的方法,如：查询、插入、更新、删除等）还有数据域属性。 
SqlSession最佳应用场合在方法体内，定义成局部变量使用。

2 SqlSessionFactoryBuilder

SqlSessionFactoryBuilder用于创建SqlSessionFacoty，SqlSessionFacoty一旦创建完成就不需要SqlSessionFactoryBuilder了，因为SqlSession是通过SqlSessionFactory生产，所以可以将SqlSessionFactoryBuilder当成一个工具类使用，最佳使用范围是方法范围即方法体内局部变量。

3 SqlSessionFactory

SqlSessionFactory是一个接口，接口中定义了openSession的不同重载方法，SqlSessionFactory的最佳使用范围是整个应用运行期间，一旦创建后可以重复使用，通常以单例模式管理SqlSessionFactory。

##

---
layout: post
title: MyBatis
categories: JavaEE
tags: MyBatis
author: fidemyuan
---

##  MyBatis 简介

MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis，是一个基于Java的持久层框架。<br>

持久层： 可以将业务数据存储到磁盘，具备长期存储能力，只要磁盘不损坏，在断电或者其他情况下，重新开启系统仍然可以读取到这些数据。<br>

优点： 可以使用巨大的磁盘空间存储相当量的数据，并且很廉价<br>
缺点：慢（相对于内存而言）<br>

## 与JDBC的比较

Java程序都是通过JDBC（Java Data Base Connectivity）来访问数据库的。JDBC定义了一系列的接口规范，具体的实现是由各数据库厂商去实现，是一种典型的桥接模式。

MyBatis是一个支持普通SQL查询，存储过程和高级映射的优秀持久层框架。

###  优势

1.MyBatis使用SqlSessionFactoryBuilder来连接完成JDBC需要代码完成的数据库获取和连接，减少了代码的重复。<br>
2.JDBC将SQL语句写到代码里，属于硬编码，非常不易维护，MyBatis可以将SQL代码写入xml中，易于修改和维护。<br>
3.JDBC的resultSet需要用户自己去读取并生成对应的POJO，MyBatis的mapper会自动将执行后的结果映射到对应的Java对象中。<br>

## MyBatis 举例使用

1.首先得搭建环境<br>
	1.1下载MyBatis工程包<br>
	1.2为Idear配置环境<br>
		1.2.1下载MyBatis Plugin<br>
		1.2.2破解<br>

2.准备一个数据库<br>
	`DROP DATABASE IF EXISTS mybatis;
	CREATE DATABASE mybatis DEFAULT CHARACTER SET utf8;
	
	use mybatis;
	CREATE TABLE student(
	  id int(11) NOT NULL AUTO_INCREMENT,
	  studentID int(11) NOT NULL UNIQUE,
	  name varchar(255) NOT NULL,
	  PRIMARY KEY (id)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES(1,1,'张三');
	INSERT INTO student VALUES(2,2,'李四');
	INSERT INTO student VALUES(3,3,'王五');`

3.创建工程然后导入必要的 jar 包：<br>

mybatis-3.4.6.jar
mysql-connector-java-5.1.21-bin.jar

4.【pojo】下新建实体类【Student】，用于映射表 student：

	`package pojo;
	
	public class Student {
	
	    int id;
	    int studentID;
	    String name;
	
	    /* getter and setter */
	}`

5.在【src】目录下创建 MyBaits 的主配置文件 mybatis-config.xml ，其主要作用是提供连接数据库用的驱动，数据名称，编码方式，账号密码等.

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	
	    <!-- 别名 -->
	    <typeAliases>
	        <package name="pojo"/>
	    </typeAliases>
	    <!-- 数据库环境 -->
	    <environments default="development">
	        <environment id="development">
	            <transactionManager type="JDBC"/>
	            <dataSource type="POOLED">
	                <property name="driver" value="com.mysql.jdbc.Driver"/>
	                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=UTF-8"/>
	                <property name="username" value="root"/>
	                <property name="password" value="root"/>
	            </dataSource>
	        </environment>
	    </environments>
	    <!-- 映射文件 -->
	    <mappers>
	        <mapper resource="pojo/Student.xml"/>
	    </mappers>
	
	</configuration>`

6.配置Student.xml,写CRUD的操作、模糊查询的操作

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="pojo">
	    <select id="listStudent" resultType="Student">
	        select * from  student
	    </select>
	
	    <insert id="addStudent" parameterType="Student">
	        insert into student (id, studentID, name) values (#{id},#{studentID},#{name})
	    </insert>
	
	    <delete id="deleteStudent" parameterType="Student">
	        delete from student where id = #{id}
	    </delete>
	
	    <select id="getStudent" parameterType="_int" resultType="Student">
	        select * from student where id= #{id}
	    </select>
	
	    <update id="updateStudent" parameterType="Student">
	        update student set name=#{name} where id=#{id}
	    </update>

		<select id="findStudentByName" parameterMap="java.lang.String" resultType="Student">
    	SELECT * FROM student WHERE name LIKE '%${value}%' 
		</select>

	</mapper>`

7.编写测试类实现上述对数据库的操作
	
	`package test;
	
	import org.apache.ibatis.io.Resources;
	import org.apache.ibatis.session.SqlSession;
	import org.apache.ibatis.session.SqlSessionFactory;
	import org.apache.ibatis.session.SqlSessionFactoryBuilder;
	import pojo.Student;
	
	import java.io.IOException;
	import java.io.InputStream;
	import java.util.List;
	
	public class TestMyBatis {
	
	    public static void main(String[] args) throws IOException {
	        // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	        String resource = "mybatis-config.xml";
	        InputStream inputStream = Resources.getResourceAsStream(resource);
	        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	        // 然后根据 sqlSessionFactory 得到 session
	        SqlSession session = sqlSessionFactory.openSession();
	
	        // 增加学生
	        Student student1 = new Student();
	        student1.setId(4);
	        student1.setStudentID(4);
	        student1.setName("新增加的学生");
	        session.insert("addStudent", student1);
	
	        // 删除学生
	        Student student2 = new Student();
	        student2.setId(1);
	        session.delete("deleteStudent", student2);
	
	        // 获取学生
	        Student student3 = session.selectOne("getStudent", 2);
	
	        // 修改学生
	        student3.setName("修改的学生");
	        session.update("updateStudent", student3);
	
	        // 最后通过 session 的 selectList() 方法调用 sql 语句 listStudent
	        List<Student> listStudent = session.selectList("listStudent");
	        for (Student student : listStudent) {
	            System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	        }
	
	        // 提交修改
	        session.commit();
	        // 关闭 session
	        session.close();
	    
			// 模糊查询
	    List<Student> students = session.selectList("findStudentByName", "李四");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
		}

	}`

关于 parameterType： 就是用来在 SQL 映射文件中指定输入参数类型的，可以指定为基本数据类型（如 int、float 等）、包装数据类型（如 String、Interger 等）以及用户自己编写的 JavaBean 封装类<br>

关于 resultType： 在加载 SQL 配置，并绑定指定输入参数和运行 SQL 之后，会得到数据库返回的响应结果，此时使用 resultType 就是用来指定数据库返回的信息对应的 Java 的数据类型。<br>

关于 “#{}” ： 在传统的 JDBC 的编程中，占位符用 “?” 来表示，然后再加载 SQL 之前按照 “?” 的位置设置参数。而 “#{}” 在 MyBatis 中也代表一种占位符，该符号接受输入参数，在大括号中编写参数名称来接受对应参数。当 “#{}” 接受简单类型时可以用 value 或者其他任意名称来获取。<br>

关于 “${}” ： 在 SQL 配置中，有时候需要拼接 SQL 语句（例如模糊查询时），用 “#{}” 是无法达到目的的。在 MyBatis 中，“${}” 代表一个 “拼接符号” ，可以在原有 SQL 语句上拼接新的符合 
SQL 语法的语句。使用 “${}” 拼接符号拼接 SQL ，会引起 SQL 注入，所以一般不建议使用 “${}”。<br>

MyBatis 使用场景： 通过上面的入门程序，不难看出在进行 MyBatis 开发时，我们的大部分精力都放在了 SQL 映射文件上。<br>

MyBatis 的特点就是以 SQL 语句为核心的不完全的 ORM（关系型映射）框架。与 Hibernate 相比，Hibernate 的学习成本比较高，而 SQL 语句并不需要开发人员完成，只需要调用相关 API 即可。这对于开发效率是一个优势，但是缺点是没办法对 SQL 语句进行优化和修改。而 MyBatis 虽然需要开发人员自己配置 SQL 语句，MyBatis 来实现映射关系，但是这样的项目可以适应经常变化的项目需求。所以使用 MyBatis 的场景是：对 SQL 优化要求比较高，或是项目需求或业务经常变动。<br>


##  基本原理

上述对数据库操作的基本原理：

应用程序找 MyBatis 要数据<br>
MyBatis 从数据库中找来数据<br>
1.通过 mybatis-config.xml 定位哪个数据库<br>
2.通过 Student.xml 执行对应的 sql 语句<br>
3.基于 Student.xml 把返回的数据库封装在 Student 对象中<br>
4.把多个 Student 对象装载一个 Student 集合中<br>
返回一个 Student 集合<br>


## 编写日志输出环境配置文件

在开发过程中，最重要的就是在控制台查看程序输出的日志信息，在这里我们选择使用 log4j 工具来输出：<br>

将【MyBatis】文件夹下【lib】中的 log4j 开头的 jar 包都导入工程并添加依赖。<br>
在【src】下新建一个文件 log4j.properties 资源:<br>

	`# Global logging configuration
	# 在开发环境下日志级别要设置成 DEBUG ，生产环境设为 INFO 或 ERROR
	log4j.rootLogger=DEBUG, stdout
	# Console output...
	log4j.appender.stdout=org.apache.log4j.ConsoleAppender
	log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
	log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n`

1.其中，第一条配置语句 “log4j.rootLogger=DEBUG, stdout” 指的是日志输出级别，一共有 7 个级别（OFF、 FATAL、 ERROR、 WARN、 INFO、 DEBUG、 ALL）。<br>

一般常用的日志输出级别分别为 DEBUG、 INFO、 ERROR 以及 WARN，分别表示 “调试级别”、 “标准信息级别”、 “错误级别”、 “异常级别”。如果需要查看程序运行的详细步骤信息，一般选择 “DEBUG” 级别，因为该级别在程序运行期间，会在控制台才打印出底层的运行信息，以及在程序中使用 Log 对象打印出调试信息。<br>

如果是日常的运行，选择 “INFO” 级别，该级别会在控制台打印出程序运行的主要步骤信息。“ERROR” 和 “WARN” 级别分别代表 “不影响程序运行的错误事件” 和 “潜在的错误情形”。<br>

文件中 “stdout” 这段配置的意思就是将 DEBUG 的日志信息输出到 stdout 参数所指定的输出载体中。<br>

2.第二条配置语句 “log4j.appender.stdout=org.apache.log4j.ConsoleAppender” 的含义是，设置名为 stdout 的输出端载体是哪种类型。<br>

目前输出载体有<br>
ConsoleAppender（控制台）<br>
FileAppender（文件）<br>
DailyRollingFileAppender（每天产生一个日志文件）<br>
RollingFileAppender（文件大小到达指定大小时产生一个新的文件）<br>
WriterAppender（将日志信息以流格式发送到任意指定的地方）<br>
这里要将日志打印到 IDEA 的控制台，所以选择 ConsoleAppender<br>

3.第三条配置语句 “log4j.appender.stdout.layout=org.apache.log4j.PatternLayout” 的含义是，名为 stdout 的输出载体的 layout（即界面布局）是哪种类型。<br>

目前输出端的界面类型分为<br>
HTMLLayout（以 HTML 表格形式布局）<br>
PatternLayout（可以灵活地指定布局模式）<br>
SimpleLayout（包含日志信息的级别和信息字符串）<br>
TTCCLayout（包含日志产生的时间、线程、类别等信息）<br>
这里选择灵活指定其布局类型，即自己去配置布局。<br>

4.第四条配置语句 “log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n” 的含义是，如果 layout 界面布局选择了 PatternLayout 灵活布局类型，要指定的打印信息的具体格式。<br>

格式信息配置元素大致如下：<br>
%m 输出代码中的指定的信息<br>
%p 输出优先级，即 DEBUG、 INFO、 WARN、 ERROR 和 FATAL<br>
%r 输出自应用启动到输出该 log 信息耗费的毫秒数<br>
%c 输出所属的类目，通常就是所在类的全名<br>
%t 输出产生该日志事件的线程名<br>
%n 输出一个回车换行符，Windows 平台为 “rn”，UNIX 平台为 “n”<br>
%d 输出日志时的时间或日期，默认个事为 ISO8601，也可以在其后指定格式，比如 %d{yyy MMM dd HH:mm:ss}，输出类似：2018 年 4 月18 日 10:32:00<br>
%l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数<br>

##  MyBatis高级映射

### 一对一查询

就是一张表的信息包含另一张表，比如一个学生表对应一个学号表，学生表信息里面有对应的简短的编号，而学号表中，有编号对应的学号。

1.首先数据库中建表格

	`use mybatis;
	CREATE TABLE student (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  card_id int(11) NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE card (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  number int(11)  NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1',1);
	INSERT INTO student VALUES (2,'student2',2);
	
	INSERT INTO card VALUES (1,1111);
	INSERT INTO card VALUES (2,2222);`

在日常开发中，总是先确定业务的具体 SQL ，后面再将此 SQL 配置在 Mapper 文件中,所以确定查询的SQL语句为：
	`SELECT
	    student.*,
	    card.*
	FROM
	    student,card
	WHERE student.card_id = card.id AND card.number = #{value}`

#### 使用 resultType 实现

2.创建学生 student 表所对应的 Java 实体类 Student，其中封装的属性信息为响应数据库中的字段
		`package pojo;
	
	public class Student {
	
	    int id;
	    String name;
	    int card_id;
	
	    /* getter and setter */
	}`

3.查询的结果是由 resultType 指定的，也就是只能映射一个确定的 Java 包装类，上面的 Stuent 类只包含了学生的基本信息，并没有包含 Card 的信息，所以我们要创建一个最终映射类，以 Student 类为父类，然后追加 Card 的信息：

	`package pojo;
	
	public class StudentAndCard extends Student {
	    private int number;
	
	    /* getter and setter /*
	}`

4.然后在 Student.xml 映射文件中定义 <select> 类型的查询语句 SQL 配置，将之前设计好的 SQL 语句配置进去，然后指定输出参数属性为 resultType，类型为 StudentAndCard 这个 Java 包装类：

	`<select id="findStudentByCard" parameterType="_int" resultType="Student">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>`

5.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentAndCard student = session.selectOne("findStudentByCard",1111);
	    // 获得其姓名并输出
	    System.out.println(student.getName());
	}`


#### 使用resultMap 实现

 resultMap 可以将数据字段映射到名称不一样的响应实体类属性上，重要的是，可以映射实体类中包裹的其他实体类。

2.创建一个封装了 Card 号码和 Student 实体类的 StudentWithCard 类：
	
	`package pojo;
	
	public class StudentWithCard {
	    
	    Student student;
	    int number;
	    int id;
	
	    /* getter and setter */
	}`

3.配置student.xml
	
	`<select id="findStudentByCard" parameterType="_int"resultMap="StudentInfoMap">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>
	
	<resultMap id="StudentInfoMap" type="pojo.StudentWithCard">
	    <!-- id 标签表示对应的主键
	         column 对应查询结果的列值
	         property 对应封装类中的属性名称
	         -->
	    <id column="id" property="id"/>
	    <result column="number" property="number"/>
	    <!-- association 表示关联的嵌套结果，
	         可以简单理解就是为封装类指定的标签 
	         -->
	    <association property="student" javaType="pojo.Student">
	        <id column="id" property="id"/>
	        <result column="name" property="name"/>
	        <result column="card_id" property="card_id"/>
	    </association>
	</resultMap>`

4.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentWithCard student = session.selectOne("findStudentByCard", 1111);
	    // 获得其姓名并输出
	    System.out.println(student.getStudent().getName());
	}`


### 一对多查询

所谓一对多就是，一张数据表中的字段对应了其他多张数据表信息。比如一个班级信息表中，对应了多个学生信息表。

现在要求查询某个班查询到学生列表：

1.数据库中建立数据模型

	`use mybatis;
	CREATE TABLE student (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE class (
	  class_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) NOT NULL,
	  student_id int(11)  NOT NULL,
	  PRIMARY KEY (class_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1');
	INSERT INTO student VALUES (2,'student2');
	
	INSERT INTO class VALUES (1,'Java课',1);
	INSERT INTO class VALUES (2,'Java课',2);`

确定SQL语句为：

	`SELECT 
	  student.*
	FROM
	  student, class
	WHERE student.student_id = class.student_id AND class.class_id = #{value}`

2.创建对应的实体类

	`public class Student {
	
	    private int id;
	    private String name;
	
	    /* getter and setter */
	}
	
	public class Class {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.在 Package【pojo】下新建一个【class.xml】文件完成配置：
	
	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="class">
	    <resultMap id="Students" type="pojo.Student">
	        <id column="student_id" property="id"/>
	        <result column="name" property="name"/>
	    </resultMap>
	    <select id="listStudentByClassName" parameterType="String" resultMap="Students">
	        SELECT
	          student.*
	        FROM
	          student, class
	        WHERE student.student_id = class.student_id AND class.name= #{value}
	    </select>
	</mapper>`

4.编写测试类，得到结果
	
	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 查询上Java课的全部学生
	    List<Student> students = session.selectList("listStudentByClassName", "Java课");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
	}`

### 多对多查询

比如课程跟学生之间的对应关系，课程对应多个学生，学生对应多个课程

1.在数据库中创建信息
	
	`use mybatis;
	CREATE TABLE students (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  student_name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE courses (
	  course_id int(11) NOT NULL AUTO_INCREMENT,
	  course_name varchar(255) NOT NULL,
	  PRIMARY KEY (course_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE student_select_course(
	  s_id int(11) NOT NULL,
	  c_id int(11) NOT NULL,
	  PRIMARY KEY(s_id,c_id)
	) DEFAULT CHARSET=utf8;
	
	INSERT INTO students VALUES (1,'student1');
	INSERT INTO students VALUES (2,'student2');
	
	INSERT INTO courses VALUES (1,'Java课');
	INSERT INTO courses VALUES (2,'Java Web课');
	
	INSERT INTO student_select_course VALUES(1,1);
	INSERT INTO student_select_course VALUES(1,2);
	INSERT INTO student_select_course VALUES(2,1);
	INSERT INTO student_select_course VALUES(2,2);`

SQL语句可设计为：

	`SELECT
	    s.student_id,s.student_name
	FROM
	    students s,student_select_course ssc,courses c
	WHERE s.student_id = ssc.s_id 
	AND ssc.c_id = c.course_id 
	AND c.course_name = #{value}`

2.编写实体类

	`public class Student {
	
	    private int id;
	    private String name;
		private List<Core> students;
	
	    /* getter and setter */
	}
	
	public class Core {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.配置映射文件

	`<resultMap id="Students" type="pojo.Student">
	    <id property="id" column="student_id"/>
	    <result column="student_name" property="name"/>
	</resultMap>
	
	<select id="findStudentsByCourseName" parameterType="String" resultMap="Students">
	    SELECT
	      s.student_id,s.student_name
	    FROM
	      students s,student_select_course ssc,courses c
	    WHERE s.student_id = ssc.s_id
	    AND ssc.c_id = c.course_id
	    AND c.course_name = #{value}
	</select>`

4.编写测试类

		`@Test
		public void test() throws IOException {
		
		    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
		    String resource = "mybatis-config.xml";
		    InputStream inputStream = Resources.getResourceAsStream(resource);
		    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		    // 然后根据 sqlSessionFactory 得到 session
		    SqlSession session = sqlSessionFactory.openSession();
		
		    // 查询上Java课的全部学生
		    List<Student> students = session.selectList("listStudentByClassName", "Java课");
		    for (Student student : students) {
		        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
		    }
		}`

## 延迟加载

什么是延迟加载？从字面上理解，就是对某一类信息的加载之前需要延迟一会儿。在 MyBatis 中，通常会进行多表联合查询，但是有的时候不会立即用到所有的联合查询结果，这时候就可以采用延迟加载的功能。<br>

功能： 延迟加载可以做到，先从单表查询，需要时再从关联表关联查询，这样就大大提高了数据库的性能，因为查询单表要比关联查询多张表速度快。<br>

实例： 如果查询订单并且关联查询用户信息。如果先查询订单信息即可满足要求，当我们需要查询用户信息时再查询用户信息。把对用户信息的按需去查询就是延迟加载。<br>

关联查询：

	`SELECT 
	    orders.*, user.username 
	FROM orders, user
	WHERE orders.user_id = user.id
	延迟加载相当于：
	SELECT 
	    orders.*,
	    (SELECT username FROM USER WHERE orders.user_id = user.id)
	    username 
	FROM orders`

所以这就比较直观了，也就是说，我把关联查询分两次来做，而不是一次性查出所有的。第一步只查询单表orders，必然会查出orders中的一个user_id字段，然后我再根据这个user_id查user表，也是单表查询。<br>

### Mapper 映射配置编写

`<select id="findOrdersUserLazyLoading" resultMap="OrdersUserLazyLoadingResultMap">
    SELECT * FROM orders
</select>`


## Mapper动态代理

通过MyBatis 开发DAO，通常有两种方法：DAO方法和Mapper动态代理方法。

1 SqlSession的使用范围<br>

通过SqlSessionFactory创建SqlSession，而SqlSessionFactory是通过SqlSessionFactoryBuilder进行创建。 
SqlSession是一个面向用户的接口， sqlSession中定义了数据库操作，如：selectOne(返回单个对象)、selectList（返回单个或多个对象）。默认使用DefaultSqlSession实现类。 
SqlSession是线程不安全的，在SqlSesion实现类中除了有接口中的方法（操作数据库的方法,如：查询、插入、更新、删除等）还有数据域属性。 
SqlSession最佳应用场合在方法体内，定义成局部变量使用。

2 SqlSessionFactoryBuilder

SqlSessionFactoryBuilder用于创建SqlSessionFacoty，SqlSessionFacoty一旦创建完成就不需要SqlSessionFactoryBuilder了，因为SqlSession是通过SqlSessionFactory生产，所以可以将SqlSessionFactoryBuilder当成一个工具类使用，最佳使用范围是方法范围即方法体内局部变量。

3 SqlSessionFactory

SqlSessionFactory是一个接口，接口中定义了openSession的不同重载方法，SqlSessionFactory的最佳使用范围是整个应用运行期间，一旦创建后可以重复使用，通常以单例模式管理SqlSessionFactory。

##

---
layout: post
title: MyBatis
categories: JavaEE
tags: MyBatis
author: fidemyuan
---

##  MyBatis 简介

MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis，是一个基于Java的持久层框架。<br>

持久层： 可以将业务数据存储到磁盘，具备长期存储能力，只要磁盘不损坏，在断电或者其他情况下，重新开启系统仍然可以读取到这些数据。<br>

优点： 可以使用巨大的磁盘空间存储相当量的数据，并且很廉价<br>
缺点：慢（相对于内存而言）<br>

## 与JDBC的比较

Java程序都是通过JDBC（Java Data Base Connectivity）来访问数据库的。JDBC定义了一系列的接口规范，具体的实现是由各数据库厂商去实现，是一种典型的桥接模式。

MyBatis是一个支持普通SQL查询，存储过程和高级映射的优秀持久层框架。

###  优势

1.MyBatis使用SqlSessionFactoryBuilder来连接完成JDBC需要代码完成的数据库获取和连接，减少了代码的重复。<br>
2.JDBC将SQL语句写到代码里，属于硬编码，非常不易维护，MyBatis可以将SQL代码写入xml中，易于修改和维护。<br>
3.JDBC的resultSet需要用户自己去读取并生成对应的POJO，MyBatis的mapper会自动将执行后的结果映射到对应的Java对象中。<br>

## MyBatis 举例使用

1.首先得搭建环境<br>
	1.1下载MyBatis工程包<br>
	1.2为Idear配置环境<br>
		1.2.1下载MyBatis Plugin<br>
		1.2.2破解<br>

2.准备一个数据库<br>
	`DROP DATABASE IF EXISTS mybatis;
	CREATE DATABASE mybatis DEFAULT CHARACTER SET utf8;
	
	use mybatis;
	CREATE TABLE student(
	  id int(11) NOT NULL AUTO_INCREMENT,
	  studentID int(11) NOT NULL UNIQUE,
	  name varchar(255) NOT NULL,
	  PRIMARY KEY (id)
	) ENGINE=InnoDB DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES(1,1,'张三');
	INSERT INTO student VALUES(2,2,'李四');
	INSERT INTO student VALUES(3,3,'王五');`

3.创建工程然后导入必要的 jar 包：<br>

mybatis-3.4.6.jar
mysql-connector-java-5.1.21-bin.jar

4.【pojo】下新建实体类【Student】，用于映射表 student：

	`package pojo;
	
	public class Student {
	
	    int id;
	    int studentID;
	    String name;
	
	    /* getter and setter */
	}`

5.在【src】目录下创建 MyBaits 的主配置文件 mybatis-config.xml ，其主要作用是提供连接数据库用的驱动，数据名称，编码方式，账号密码等.

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
	<configuration>
	
	    <!-- 别名 -->
	    <typeAliases>
	        <package name="pojo"/>
	    </typeAliases>
	    <!-- 数据库环境 -->
	    <environments default="development">
	        <environment id="development">
	            <transactionManager type="JDBC"/>
	            <dataSource type="POOLED">
	                <property name="driver" value="com.mysql.jdbc.Driver"/>
	                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=UTF-8"/>
	                <property name="username" value="root"/>
	                <property name="password" value="root"/>
	            </dataSource>
	        </environment>
	    </environments>
	    <!-- 映射文件 -->
	    <mappers>
	        <mapper resource="pojo/Student.xml"/>
	    </mappers>
	
	</configuration>`

6.配置Student.xml,写CRUD的操作、模糊查询的操作

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="pojo">
	    <select id="listStudent" resultType="Student">
	        select * from  student
	    </select>
	
	    <insert id="addStudent" parameterType="Student">
	        insert into student (id, studentID, name) values (#{id},#{studentID},#{name})
	    </insert>
	
	    <delete id="deleteStudent" parameterType="Student">
	        delete from student where id = #{id}
	    </delete>
	
	    <select id="getStudent" parameterType="_int" resultType="Student">
	        select * from student where id= #{id}
	    </select>
	
	    <update id="updateStudent" parameterType="Student">
	        update student set name=#{name} where id=#{id}
	    </update>

		<select id="findStudentByName" parameterMap="java.lang.String" resultType="Student">
    	SELECT * FROM student WHERE name LIKE '%${value}%' 
		</select>

	</mapper>`

7.编写测试类实现上述对数据库的操作
	
	`package test;
	
	import org.apache.ibatis.io.Resources;
	import org.apache.ibatis.session.SqlSession;
	import org.apache.ibatis.session.SqlSessionFactory;
	import org.apache.ibatis.session.SqlSessionFactoryBuilder;
	import pojo.Student;
	
	import java.io.IOException;
	import java.io.InputStream;
	import java.util.List;
	
	public class TestMyBatis {
	
	    public static void main(String[] args) throws IOException {
	        // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	        String resource = "mybatis-config.xml";
	        InputStream inputStream = Resources.getResourceAsStream(resource);
	        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	        // 然后根据 sqlSessionFactory 得到 session
	        SqlSession session = sqlSessionFactory.openSession();
	
	        // 增加学生
	        Student student1 = new Student();
	        student1.setId(4);
	        student1.setStudentID(4);
	        student1.setName("新增加的学生");
	        session.insert("addStudent", student1);
	
	        // 删除学生
	        Student student2 = new Student();
	        student2.setId(1);
	        session.delete("deleteStudent", student2);
	
	        // 获取学生
	        Student student3 = session.selectOne("getStudent", 2);
	
	        // 修改学生
	        student3.setName("修改的学生");
	        session.update("updateStudent", student3);
	
	        // 最后通过 session 的 selectList() 方法调用 sql 语句 listStudent
	        List<Student> listStudent = session.selectList("listStudent");
	        for (Student student : listStudent) {
	            System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	        }
	
	        // 提交修改
	        session.commit();
	        // 关闭 session
	        session.close();
	    
			// 模糊查询
	    List<Student> students = session.selectList("findStudentByName", "李四");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
		}

	}`

关于 parameterType： 就是用来在 SQL 映射文件中指定输入参数类型的，可以指定为基本数据类型（如 int、float 等）、包装数据类型（如 String、Interger 等）以及用户自己编写的 JavaBean 封装类<br>

关于 resultType： 在加载 SQL 配置，并绑定指定输入参数和运行 SQL 之后，会得到数据库返回的响应结果，此时使用 resultType 就是用来指定数据库返回的信息对应的 Java 的数据类型。<br>

关于 “#{}” ： 在传统的 JDBC 的编程中，占位符用 “?” 来表示，然后再加载 SQL 之前按照 “?” 的位置设置参数。而 “#{}” 在 MyBatis 中也代表一种占位符，该符号接受输入参数，在大括号中编写参数名称来接受对应参数。当 “#{}” 接受简单类型时可以用 value 或者其他任意名称来获取。<br>

关于 “${}” ： 在 SQL 配置中，有时候需要拼接 SQL 语句（例如模糊查询时），用 “#{}” 是无法达到目的的。在 MyBatis 中，“${}” 代表一个 “拼接符号” ，可以在原有 SQL 语句上拼接新的符合 
SQL 语法的语句。使用 “${}” 拼接符号拼接 SQL ，会引起 SQL 注入，所以一般不建议使用 “${}”。<br>

MyBatis 使用场景： 通过上面的入门程序，不难看出在进行 MyBatis 开发时，我们的大部分精力都放在了 SQL 映射文件上。<br>

MyBatis 的特点就是以 SQL 语句为核心的不完全的 ORM（关系型映射）框架。与 Hibernate 相比，Hibernate 的学习成本比较高，而 SQL 语句并不需要开发人员完成，只需要调用相关 API 即可。这对于开发效率是一个优势，但是缺点是没办法对 SQL 语句进行优化和修改。而 MyBatis 虽然需要开发人员自己配置 SQL 语句，MyBatis 来实现映射关系，但是这样的项目可以适应经常变化的项目需求。所以使用 MyBatis 的场景是：对 SQL 优化要求比较高，或是项目需求或业务经常变动。<br>


##  基本原理

上述对数据库操作的基本原理：

应用程序找 MyBatis 要数据<br>
MyBatis 从数据库中找来数据<br>
1.通过 mybatis-config.xml 定位哪个数据库<br>
2.通过 Student.xml 执行对应的 sql 语句<br>
3.基于 Student.xml 把返回的数据库封装在 Student 对象中<br>
4.把多个 Student 对象装载一个 Student 集合中<br>
返回一个 Student 集合<br>


## 编写日志输出环境配置文件

在开发过程中，最重要的就是在控制台查看程序输出的日志信息，在这里我们选择使用 log4j 工具来输出：<br>

将【MyBatis】文件夹下【lib】中的 log4j 开头的 jar 包都导入工程并添加依赖。<br>
在【src】下新建一个文件 log4j.properties 资源:<br>

	`# Global logging configuration
	# 在开发环境下日志级别要设置成 DEBUG ，生产环境设为 INFO 或 ERROR
	log4j.rootLogger=DEBUG, stdout
	# Console output...
	log4j.appender.stdout=org.apache.log4j.ConsoleAppender
	log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
	log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n`

1.其中，第一条配置语句 “log4j.rootLogger=DEBUG, stdout” 指的是日志输出级别，一共有 7 个级别（OFF、 FATAL、 ERROR、 WARN、 INFO、 DEBUG、 ALL）。<br>

一般常用的日志输出级别分别为 DEBUG、 INFO、 ERROR 以及 WARN，分别表示 “调试级别”、 “标准信息级别”、 “错误级别”、 “异常级别”。如果需要查看程序运行的详细步骤信息，一般选择 “DEBUG” 级别，因为该级别在程序运行期间，会在控制台才打印出底层的运行信息，以及在程序中使用 Log 对象打印出调试信息。<br>

如果是日常的运行，选择 “INFO” 级别，该级别会在控制台打印出程序运行的主要步骤信息。“ERROR” 和 “WARN” 级别分别代表 “不影响程序运行的错误事件” 和 “潜在的错误情形”。<br>

文件中 “stdout” 这段配置的意思就是将 DEBUG 的日志信息输出到 stdout 参数所指定的输出载体中。<br>

2.第二条配置语句 “log4j.appender.stdout=org.apache.log4j.ConsoleAppender” 的含义是，设置名为 stdout 的输出端载体是哪种类型。<br>

目前输出载体有<br>
ConsoleAppender（控制台）<br>
FileAppender（文件）<br>
DailyRollingFileAppender（每天产生一个日志文件）<br>
RollingFileAppender（文件大小到达指定大小时产生一个新的文件）<br>
WriterAppender（将日志信息以流格式发送到任意指定的地方）<br>
这里要将日志打印到 IDEA 的控制台，所以选择 ConsoleAppender<br>

3.第三条配置语句 “log4j.appender.stdout.layout=org.apache.log4j.PatternLayout” 的含义是，名为 stdout 的输出载体的 layout（即界面布局）是哪种类型。<br>

目前输出端的界面类型分为<br>
HTMLLayout（以 HTML 表格形式布局）<br>
PatternLayout（可以灵活地指定布局模式）<br>
SimpleLayout（包含日志信息的级别和信息字符串）<br>
TTCCLayout（包含日志产生的时间、线程、类别等信息）<br>
这里选择灵活指定其布局类型，即自己去配置布局。<br>

4.第四条配置语句 “log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n” 的含义是，如果 layout 界面布局选择了 PatternLayout 灵活布局类型，要指定的打印信息的具体格式。<br>

格式信息配置元素大致如下：<br>
%m 输出代码中的指定的信息<br>
%p 输出优先级，即 DEBUG、 INFO、 WARN、 ERROR 和 FATAL<br>
%r 输出自应用启动到输出该 log 信息耗费的毫秒数<br>
%c 输出所属的类目，通常就是所在类的全名<br>
%t 输出产生该日志事件的线程名<br>
%n 输出一个回车换行符，Windows 平台为 “rn”，UNIX 平台为 “n”<br>
%d 输出日志时的时间或日期，默认个事为 ISO8601，也可以在其后指定格式，比如 %d{yyy MMM dd HH:mm:ss}，输出类似：2018 年 4 月18 日 10:32:00<br>
%l 输出日志事件的发生位置，包括类目名、发生的线程，以及在代码中的行数<br>

##  MyBatis高级映射

### 一对一查询

就是一张表的信息包含另一张表，比如一个学生表对应一个学号表，学生表信息里面有对应的简短的编号，而学号表中，有编号对应的学号。

1.首先数据库中建表格

	`use mybatis;
	CREATE TABLE student (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  card_id int(11) NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE card (
	  id int(11) NOT NULL AUTO_INCREMENT,
	  number int(11)  NOT NULL,
	  PRIMARY KEY (id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1',1);
	INSERT INTO student VALUES (2,'student2',2);
	
	INSERT INTO card VALUES (1,1111);
	INSERT INTO card VALUES (2,2222);`

在日常开发中，总是先确定业务的具体 SQL ，后面再将此 SQL 配置在 Mapper 文件中,所以确定查询的SQL语句为：
	`SELECT
	    student.*,
	    card.*
	FROM
	    student,card
	WHERE student.card_id = card.id AND card.number = #{value}`

#### 使用 resultType 实现

2.创建学生 student 表所对应的 Java 实体类 Student，其中封装的属性信息为响应数据库中的字段
		`package pojo;
	
	public class Student {
	
	    int id;
	    String name;
	    int card_id;
	
	    /* getter and setter */
	}`

3.查询的结果是由 resultType 指定的，也就是只能映射一个确定的 Java 包装类，上面的 Stuent 类只包含了学生的基本信息，并没有包含 Card 的信息，所以我们要创建一个最终映射类，以 Student 类为父类，然后追加 Card 的信息：

	`package pojo;
	
	public class StudentAndCard extends Student {
	    private int number;
	
	    /* getter and setter /*
	}`

4.然后在 Student.xml 映射文件中定义 <select> 类型的查询语句 SQL 配置，将之前设计好的 SQL 语句配置进去，然后指定输出参数属性为 resultType，类型为 StudentAndCard 这个 Java 包装类：

	`<select id="findStudentByCard" parameterType="_int" resultType="Student">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>`

5.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentAndCard student = session.selectOne("findStudentByCard",1111);
	    // 获得其姓名并输出
	    System.out.println(student.getName());
	}`


#### 使用resultMap 实现

 resultMap 可以将数据字段映射到名称不一样的响应实体类属性上，重要的是，可以映射实体类中包裹的其他实体类。

2.创建一个封装了 Card 号码和 Student 实体类的 StudentWithCard 类：
	
	`package pojo;
	
	public class StudentWithCard {
	    
	    Student student;
	    int number;
	    int id;
	
	    /* getter and setter */
	}`

3.配置student.xml
	
	`<select id="findStudentByCard" parameterType="_int"resultMap="StudentInfoMap">
	  SELECT
	    student.*,
	    card.*
	  FROM
	    student,card
	  WHERE student.card_id = card.id AND card.number = #{value}
	</select>
	
	<resultMap id="StudentInfoMap" type="pojo.StudentWithCard">
	    <!-- id 标签表示对应的主键
	         column 对应查询结果的列值
	         property 对应封装类中的属性名称
	         -->
	    <id column="id" property="id"/>
	    <result column="number" property="number"/>
	    <!-- association 表示关联的嵌套结果，
	         可以简单理解就是为封装类指定的标签 
	         -->
	    <association property="student" javaType="pojo.Student">
	        <id column="id" property="id"/>
	        <result column="name" property="name"/>
	        <result column="card_id" property="card_id"/>
	    </association>
	</resultMap>`

4.编写测试类,得到结果

	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 找到身份证身份证号码为 1111 的学生
	    StudentWithCard student = session.selectOne("findStudentByCard", 1111);
	    // 获得其姓名并输出
	    System.out.println(student.getStudent().getName());
	}`


### 一对多查询

所谓一对多就是，一张数据表中的字段对应了其他多张数据表信息。比如一个班级信息表中，对应了多个学生信息表。

现在要求查询某个班查询到学生列表：

1.数据库中建立数据模型

	`use mybatis;
	CREATE TABLE student (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE class (
	  class_id int(11) NOT NULL AUTO_INCREMENT,
	  name varchar(255) NOT NULL,
	  student_id int(11)  NOT NULL,
	  PRIMARY KEY (class_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	INSERT INTO student VALUES (1,'student1');
	INSERT INTO student VALUES (2,'student2');
	
	INSERT INTO class VALUES (1,'Java课',1);
	INSERT INTO class VALUES (2,'Java课',2);`

确定SQL语句为：

	`SELECT 
	  student.*
	FROM
	  student, class
	WHERE student.student_id = class.student_id AND class.class_id = #{value}`

2.创建对应的实体类

	`public class Student {
	
	    private int id;
	    private String name;
	
	    /* getter and setter */
	}
	
	public class Class {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.在 Package【pojo】下新建一个【class.xml】文件完成配置：
	
	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="class">
	    <resultMap id="Students" type="pojo.Student">
	        <id column="student_id" property="id"/>
	        <result column="name" property="name"/>
	    </resultMap>
	    <select id="listStudentByClassName" parameterType="String" resultMap="Students">
	        SELECT
	          student.*
	        FROM
	          student, class
	        WHERE student.student_id = class.student_id AND class.name= #{value}
	    </select>
	</mapper>`

4.编写测试类，得到结果
	
	`@Test
	public void test() throws IOException {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	
	    // 查询上Java课的全部学生
	    List<Student> students = session.selectList("listStudentByClassName", "Java课");
	    for (Student student : students) {
	        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
	    }
	}`

### 多对多查询

比如课程跟学生之间的对应关系，课程对应多个学生，学生对应多个课程

1.在数据库中创建信息
	
	`use mybatis;
	CREATE TABLE students (
	  student_id int(11) NOT NULL AUTO_INCREMENT,
	  student_name varchar(255) DEFAULT NULL,
	  PRIMARY KEY (student_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE courses (
	  course_id int(11) NOT NULL AUTO_INCREMENT,
	  course_name varchar(255) NOT NULL,
	  PRIMARY KEY (course_id)
	)AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
	
	CREATE TABLE student_select_course(
	  s_id int(11) NOT NULL,
	  c_id int(11) NOT NULL,
	  PRIMARY KEY(s_id,c_id)
	) DEFAULT CHARSET=utf8;
	
	INSERT INTO students VALUES (1,'student1');
	INSERT INTO students VALUES (2,'student2');
	
	INSERT INTO courses VALUES (1,'Java课');
	INSERT INTO courses VALUES (2,'Java Web课');
	
	INSERT INTO student_select_course VALUES(1,1);
	INSERT INTO student_select_course VALUES(1,2);
	INSERT INTO student_select_course VALUES(2,1);
	INSERT INTO student_select_course VALUES(2,2);`

SQL语句可设计为：

	`SELECT
	    s.student_id,s.student_name
	FROM
	    students s,student_select_course ssc,courses c
	WHERE s.student_id = ssc.s_id 
	AND ssc.c_id = c.course_id 
	AND c.course_name = #{value}`

2.编写实体类

	`public class Student {
	
	    private int id;
	    private String name;
		private List<Core> students;
	
	    /* getter and setter */
	}
	
	public class Core {
	
	    private int id;
	    private String name;
	    private List<Student> students;
	
	    /* getter and setter */
	}`

3.配置映射文件

	`<resultMap id="Students" type="pojo.Student">
	    <id property="id" column="student_id"/>
	    <result column="student_name" property="name"/>
	</resultMap>
	
	<select id="findStudentsByCourseName" parameterType="String" resultMap="Students">
	    SELECT
	      s.student_id,s.student_name
	    FROM
	      students s,student_select_course ssc,courses c
	    WHERE s.student_id = ssc.s_id
	    AND ssc.c_id = c.course_id
	    AND c.course_name = #{value}
	</select>`

4.编写测试类

		`@Test
		public void test() throws IOException {
		
		    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
		    String resource = "mybatis-config.xml";
		    InputStream inputStream = Resources.getResourceAsStream(resource);
		    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
		    // 然后根据 sqlSessionFactory 得到 session
		    SqlSession session = sqlSessionFactory.openSession();
		
		    // 查询上Java课的全部学生
		    List<Student> students = session.selectList("listStudentByClassName", "Java课");
		    for (Student student : students) {
		        System.out.println("ID:" + student.getId() + ",NAME:" + student.getName());
		    }
		}`

## 延迟加载

什么是延迟加载？从字面上理解，就是对某一类信息的加载之前需要延迟一会儿。在 MyBatis 中，通常会进行多表联合查询，但是有的时候不会立即用到所有的联合查询结果，这时候就可以采用延迟加载的功能。<br>

功能： 延迟加载可以做到，先从单表查询，需要时再从关联表关联查询，这样就大大提高了数据库的性能，因为查询单表要比关联查询多张表速度快。<br>

实例： 如果查询订单并且关联查询用户信息。如果先查询订单信息即可满足要求，当我们需要查询用户信息时再查询用户信息。把对用户信息的按需去查询就是延迟加载。<br>

关联查询：

	`SELECT 
	    orders.*, user.username 
	FROM orders, user
	WHERE orders.user_id = user.id
	延迟加载相当于：
	SELECT 
	    orders.*,
	    (SELECT username FROM USER WHERE orders.user_id = user.id)
	    username 
	FROM orders`

所以这就比较直观了，也就是说，我把关联查询分两次来做，而不是一次性查出所有的。第一步只查询单表orders，必然会查出orders中的一个user_id字段，然后我再根据这个user_id查user表，也是单表查询。<br>

### Mapper 映射配置编写

	`<select id="findOrdersUserLazyLoading" resultMap="OrdersUserLazyLoadingResultMap">
	    SELECT * FROM orders
	</select>`


## Mapper动态代理

通过MyBatis 开发DAO，通常有两种方法：DAO方法和Mapper动态代理方法。<br>

1 SqlSession的使用范围<br>

通过SqlSessionFactory创建SqlSession，而SqlSessionFactory是通过SqlSessionFactoryBuilder进行创建。 <br>
SqlSession是一个面向用户的接口， sqlSession中定义了数据库操作，如：selectOne(返回单个对象)、selectList（返回单个或多个对象）。默认使用DefaultSqlSession实现类。 <br>
SqlSession是线程不安全的，在SqlSesion实现类中除了有接口中的方法（操作数据库的方法,如：查询、插入、更新、删除等）还有数据域属性。 <br>
SqlSession最佳应用场合在方法体内，定义成局部变量使用。<br>

2 SqlSessionFactoryBuilder<br>

SqlSessionFactoryBuilder用于创建SqlSessionFacoty，SqlSessionFacoty一旦创建完成就不需要SqlSessionFactoryBuilder了，因为SqlSession是通过SqlSessionFactory生产，所以可以将SqlSessionFactoryBuilder当成一个工具类使用，最佳使用范围是方法范围即方法体内局部变量。<br>

3 SqlSessionFactory<br>

SqlSessionFactory是一个接口，接口中定义了openSession的不同重载方法，SqlSessionFactory的最佳使用范围是整个应用运行期间，一旦创建后可以重复使用，通常以单例模式管理SqlSessionFactory。<br>

### 原始DAO开发方法

程序员需要写dao接口和dao实现类。 <br>
需要向dao实现类中注入SqlSessionFactory，在方法体内通过SqlSessionFactory创建SqlSession<br>

1.创建接口<br>

	`public interface UserDao {
	    public User findUserById(int id) throws Exception;
	    public void insertUser(User user) throws Exception;
	    public void deleteUser(int id) throws Exception;
	}`

2.dao接口实现类

	`public class UserDaoImpl implements UserDao{
	
	    //需要向dao实现类中注入SqlSessionFactory
	    //这里通过构造方法注入
	    private SqlSessionFactory sqlSessionFactory;
	    public UserDaoImpl(SqlSessionFactory sqlSessionFactory){
	        this.sqlSessionFactory=sqlSessionFactory;
	    }
	    @Override
	    public User findUserById(int id) throws Exception {
	        SqlSession sqlSession=sqlSessionFactory.openSession();
	        User user=sqlSession.selectOne("test.findUserById",id);
	
	        //释放资源
	        sqlSession.close();
	        return user;
	    }
	
	    @Override
	    public void insertUser(User user) throws Exception {
	        SqlSession sqlSession=sqlSessionFactory.openSession();
	
	        sqlSession.insert("test.insertUser",user);
	
	        sqlSession.commit();
	        sqlSession.close();
	
	    }
	
	    @Override
	    public void deleteUser(int id) throws Exception {
	        SqlSession sqlSession=sqlSessionFactory.openSession();
	
	        sqlSession.delete("test.deleteUser",id);
	
	        sqlSession.commit();
	        sqlSession.close();
	    }
	
	}`

3.编写测试类
	
	`public class testDao {
	    private SqlSessionFactory sqlSessionFactory;
	    @Before       //@Before
	    public void setUp() throws Exception{
	        //mybatis配置文件
	        String resource="SqlMapConfig.xml";
	        //得到配置文件流
	        InputStream inputStream =Resources.getResourceAsStream(resource);
	        //创建会话工厂，传入mybatis的配置文件信息
	        sqlSessionFactory=new SqlSessionFactoryBuilder().build(inputStream);
	    }
	    @Test
	    public void testFindUserById() throws Exception{
	        //创建UserDao对象
	        UserDao userDao=new UserDaoImpl(sqlSessionFactory);
	        //调用UserDao的方法
	        User user=userDao.findUserById(1);
	        System.out.println(user);
	    }
	
	    @Test
	    public void testinsertUser() throws Exception{
	        //创建UserDao对象
	        UserDao userDao=new UserDaoImpl(sqlSessionFactory);
	        User user=new User();
	        //必须设置id
	        user.setUid(2);
	        user.setLoginname("斯芬");
	        user.setPassword("345");
	        user.setSex("男");
	        user.setAge(23);
	        //调用UserDao的方法
	        userDao.insertUser(user);
	    }
	}`


dao接口开发存在的问题：

1、dao接口实现类方法中存在大量模板方法，设想能否将这些代码提取出来，大大减轻程序员的工作量。 
2、调用sqlsession方法时将statement的id硬编码了 
3、调用sqlsession方法时传入的变量，由于sqlsession方法使用泛型，即使变量类型传入错误，在编译阶段也不报错，不利于程序员开发。

### mapper代理方法（程序员只需要mapper接口（相当 于dao接口）） 

需要编写mapper.xml映射文件<br>
编写mapper接口需要遵循一些开发规范，mybatis可以自动生成mapper接口实现类代理对象。 <br>
开发规范： <br>
1、在mapper.xml中namespace等于mapper接口地址<br>
2.mapper.java接口中的方法名和mapper.xml中statement的id一致 
3、mapper.java接口中的方法输入参数类型和mapper.xml中statement的parameterType指定的类型一致。 
4、mapper.java接口中的方法返回值类型和mapper.xml中statement的resultType指定的类型一致。


实际例子：

1.编写一个使用 Mapper 代理查询学生信息的示例，首先还是在【pojo】下新建一个名为 StudentMapper.xml 的 Mapper 配置文件，其中包含了对 Student 的增删改查的 SQL 配置：

	`<?xml version="1.0" encoding="UTF-8"?>
	<!DOCTYPE mapper
	        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
	
	<mapper namespace="mapper.StudentMapper">
	    <!-- 查询学生 -->
	    <select id="findStudentById" parameterType="_int" resultType="pojo.Student">
	        SELECT * FROM student WHERE student_id = #{id}
	    </select>
	    <!-- 增加用户 -->
	    <insert id="insertStudent" parameterType="pojo.Student">
	        INSERT INTO student(student_id, name) VALUES(#{id}, #{name})
	    </insert>
	    <!-- 删除用户 -->
	    <delete id="deleteStudent" parameterType="_int">
	        DELETE FROM student WHERE student_id = #{id}
	    </delete>
	    <!-- 修改用户 -->
	    <update id="updateStudent" parameterType="pojo.Student">
	        UPDATE student SET name = #{name} WHERE student_id = #{id}
	    </update>
	</mapper>`

2.定义一个接口，名为 StudentMapper。然后在里面新建四个方法定义，分别对应 StudentMapper.xml 中的 Student 的增删改查的 SQL 配置，然后将 StudentMapper 中的 namespace 改为 StudentMapper 接口定义的地方（也就是 mapper 包下的 StudentMapper），这样就可以在业务类中使用 Mapper 代理了，接口代码如下：
	
	`package mapper;
	
	import pojo.Student;
	
	public interface StudentMapper {
	
	    // 根据 id 查询学生信息
	    public Student findStudentById(int id) throws Exception;
	
	    // 添加学生信息
	    public void insertStudent(Student student) throws Exception;
	
	    // 删除学生信息
	    public void deleteStudent(int id) throws Exception;
	
	    // 修改学生信息
	    public void updateStudent(Student student) throws Exception;
	}`

3.mybatis-config.xml 中配置一下 Mapper 映射文件
	
	`<mappers>
	        <mapper resource="pojo/StudentMapper.xml"/>
	    </mappers>`

4.测试

	`@Test
	public void test() throws Exception {
	
	    // 根据 mybatis-config.xml 配置的信息得到 sqlSessionFactory
	    String resource = "mybatis-config.xml";
	    InputStream inputStream = Resources.getResourceAsStream(resource);
	    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	    // 然后根据 sqlSessionFactory 得到 session
	    SqlSession session = sqlSessionFactory.openSession();
	    // 获取 Mapper 代理
	    StudentMapper studentMapper = session.getMapper(StudentMapper.class);
	    // 执行 Mapper 代理独享的查询方法
	    Student student = studentMapper.findStudentById(1);
	    System.out.println("学生的姓名为：" + student.getName());
	    session.close();
	}`

## 使用注解开发 MyBatis

可以进一步省掉 XML 的配置信息，进而使用方便的注解来开发 MyBatis 

1.对比上一个文件把Mapper.xml中的sql语句注入StudentMapper接口中

	`public interface StudentMapper {
	
	    // 根据 id 查询学生信息
	    @Select("SELECT * FROM student WHERE student_id = #{id}")
	    public Student findStudentById(int id) throws Exception;
	
	    // 添加学生信息
	    @Insert("INSERT INTO student(student_id, name) VALUES(#{id}, #{name})")
	    public void insertStudent(Student student) throws Exception;
	
	    // 删除学生信息
	    @Delete("DELETE FROM student WHERE student_id = #{id}")
	    public void deleteStudent(int id) throws Exception;
	
	    // 修改学生信息
	    @Update("UPDATE student SET name = #{name} WHERE student_id = #{id}")
	    public void updateStudent(Student student) throws Exception;
	}`
2.第二步：修改 mybatis-config.xml

将之前配置的映射注释掉，新建一条：

	`<!-- 映射文件 -->
	<mappers>
	    <!--<mapper resource="pojo/StudentMapper.xml"/>-->
	    <mapper class="mapper.StudentMapper"/>
	</mappers>`

3.测试

## 	MyBatis缓存结构

可以将一些变动不大且访问频率高的数据，放置在一个缓存容器中，用户下一次查询时就从缓存容器中获取结果。

### 一级缓存
一级查询存在于每一个 SqlSession 类的实例对象中，当第一次查询某一个数据时，SqlSession 类的实例对象会将该数据存入一级缓存区域，在没有收到改变该数据的请求之前，用户再次查询该数据，都会从缓存中获取该数据，而不是再次连接数据库进行查询。<br>

第一次发出一个查询 sql，sql 查询结果写入 sqlsession 的一级缓存中，缓存使用的数据结构是一个 map

key：hashcode+sql+sql输入参数+输出参数（sql的唯一标识）<br>
value：用户信息<br>

同一个 sqlsession 再次发出相同的 sql，就从缓存中取不走数据库。如果两次中间出现 commit 操作（修改、添加、删除），本 sqlsession 中的一级缓存区域全部清空，下次再去缓存中查询不到所以要从数据库查询，从数据库查询到再写入缓存。<br>

注意：
MyBatis 默认就是支持一级缓存的，并不需要我们配置.<br>
MyBatis 和 spring 整合后进行 mapper 代理开发，不支持一级缓存，mybatis和 spring 整合，spring 按照 mapper 的模板去生成 mapper 代理对象，模板中在最后统一关闭 sqlsession。<br>

### 二级缓存

二级缓存的原理和一级缓存原理一样，第一次查询，会将数据放入缓存中，然后第二次查询则会直接去缓存中取。但是一级缓存是基于 sqlSession 的，而 二级缓存是基于 mapper文件的namespace的，也就是说多个sqlSession可以共享一个mapper中的二级缓存区域，并且如果两个mapper的namespace相同，即使是两个mapper，那么这两个mapper中执行sql查询到的数据也将存在相同的二级缓存区域中。<br>

如何开启二级缓存：

1.在 MyBatis 的全局配置文件 mybatis-config.xml 中配置 setting 属性，设置名为 “cacheEnable” 的属性值为 “true” 即可：

	`<settings>
	    <!-- 开启二级缓存 -->
	    <setting name="cacheEnabled" value="true"/>
	</settings>`

2.然后由于二级缓存是 Mapper 级别的，还要在需要开启二级缓存的具体 mapper.xml 文件中开启二级缓存，只需要在相应的 mapper.xml 中添加一个 cache 标签即可：
	
	`<!-- 开启本 Mapper 的 namespace 下的二级缓存 -->
	<cache />`


开启二级缓存之后，我们需要为查询结果映射的 POJO 类实现 java.io.serializable 接口，二级缓存可以将内存的数据写到磁盘，存在对象的序列化和反序列化，所以要实现java.io.serializable接口。