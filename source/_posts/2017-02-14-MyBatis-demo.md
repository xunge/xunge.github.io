---
title: MyBatis 的简单案例
tags:
  - MyBatis
categories: 知识点
permalink: MyBatis-demo
updated: '2017-02-14 09:01:42'
date: 2017-02-14 09:01:42
---

一个稍复杂的 MyBatis 连结数据库的案例，涉及多表查询，以及稍复杂的 SQL 语句

共三张表：学生表 student，班级表 class，分数表 score

<!--more-->




案例为

1.查询 **张三** 的 **数学** 成绩
2.查询 **三班** 全体成员成绩
3.查询 **数学** 第4，5，6名的 **学生姓名**




## 导入 jar 包

博主使用的是 MySQL 数据库，所以需要导一些 jar 包

新建一个 java project , 在工程下新建文件夹，命名为 **lib** ，将[MySQL 的驱动文件](http://pan.baidu.com/s/1jIKxFlc) 和 [MyBatis 的驱动文件](http://pan.baidu.com/s/1b7bZ46) 复制到该文件夹，并右键 **Build Path** -> **Add to Build Path** 。这时发现在工程里出现 **Referenced Libraries** ，里面有和刚才同名的 **jar** 文件。

## 创建数据库

### 建表

建表的 sql 语句如下(为了方便没有外键)

```sql
create table class(
	classno int primary key,
    classname varchar(20)
);

create table student(
	studentno int primary key,
    studentname varchar(20),
    sex varchar(10),
    classno int
);

create table score(
	scoreid int primary key,
    studentno int,
    object varchar(20),
    score float
);
```

### 插入数据

插入数据的 sql 语句如下

```sql
INSERT INTO `class` (`classno`, `classname`) VALUES ('1', '一班');
INSERT INTO `class` (`classno`, `classname`) VALUES ('2', '二班');
INSERT INTO `class` (`classno`, `classname`) VALUES ('3', '三班');

INSERT INTO `student` (`studentno`, `studentname`, `sex`, `classno`) VALUES ('1', '张三', '男', '1');
INSERT INTO `student` (`studentno`, `studentname`, `sex`, `classno`) VALUES ('2', '李四', '女', '2');
INSERT INTO `student` (`studentno`, `studentname`, `sex`, `classno`) VALUES ('3', '王五', '女', '1');
INSERT INTO `student` (`studentno`, `studentname`, `sex`, `classno`) VALUES ('4', '吴六', '男', '2');
INSERT INTO `student` (`studentno`, `studentname`, `sex`, `classno`) VALUES ('5', '赵七', '男', '3');
INSERT INTO `student` (`studentno`, `studentname`, `sex`, `classno`) VALUES ('6', '孙八', '女', '3');

INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('1', '1', '语文', '99');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('2', '1', '数学', '98');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('3', '2', '语文', '92');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('4', '2', '数学', '94');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('5', '3', '语文', '95');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('6', '3', '数学', '93');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('7', '4', '语文', '97');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('8', '4', '数学', '96');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('9', '5', '语文', '94');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('10', '5', '数学', '93');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('11', '6', '语文', '94');
INSERT INTO `score` (`scoreid`, `studentno`, `object`, `score`) VALUES ('12', '6', '数学', '95');
```

## 配置文件连接数据库

### db.properties

首先配置连结数据库文件，在 `src` 下新建文件，名称为 `db.properties` ，内容如下，其中 `20170214` 为数据库名称，需要改成你自己的数据库名字。

```
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/20170214?useUnicode=true&amp;characterEncoding=utf-8
jdbc.username=root
jdbc.password=root
```

### SqlMapConfig.xml

在 `src` 下新建一个 `SqlMapConfig.xml` 文件，内容为：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<!-- 加载属性文件 -->
	<properties resource="db.properties"/>
	<environments default="development">
		<environment id="development">
			<!-- 事务管理类型，JDBC表示直接使用JDBC的提交和回滚设置，依赖于数据源得到的连接来管理事务 -->
			<transactionManager type="JDBC"/>
			<!-- 数据库连接池POOLED表示使用数据库连接池 -->
			<dataSource type="POOLED">
				<property name="driver" value="${jdbc.driver}"/>
				<property name="url" value="${jdbc.url}"/>
				<property name="username" value="${jdbc.username}"/>
				<property name="password" value="${jdbc.password}"/>
			</dataSource>
		</environment>
	</environments>
	<mappers>
		<package name="mapper"/>
	</mappers>
</configuration>
```

## 新建 pojo 对象类

在 `src` 下新建一个包，包名为 `pojo`，然后根据数据库列名新建 `student`, `class`, `score` 三个类，注意列名与对象名应一致，并进行set, get方法

### Class.java

```java
package pojo;
public class Class {
	private int classno;
	private String classname;
	public int getClassno() {
		return classno;
	}
	public void setClassno(int classno) {
		this.classno = classno;
	}
	public String getClassname() {
		return classname;
	}
	public void setClassname(String classname) {
		this.classname = classname;
	}
}
```

### Student.java

```java
package pojo;
public class Student {
	private int studentno;
	private String studentname;
	private String sex;
	private int classno;
	public int getStudentno() {
		return studentno;
	}
	public void setStudentno(int studentno) {
		this.studentno = studentno;
	}
	public String getStudentname() {
		return studentname;
	}
	public void setStudentname(String studentname) {
		this.studentname = studentname;
	}
	public String getSex() {
		return sex;
	}
	public void setSex(String sex) {
		this.sex = sex;
	}
	public int getClassno() {
		return classno;
	}
	public void setClassno(int classno) {
		this.classno = classno;
	}
}
```

### Score.java

```java
package pojo;
public class Score {
	private int scoreid;
	private String studentname;
	private String object;
	private float score;
	public int getScoreid() {
		return scoreid;
	}
	public void setScoreid(int scoreid) {
		this.scoreid = scoreid;
	}
	public String getStudentname() {
		return studentname;
	}
	public void setStudentname(String studentname) {
		this.studentname = studentname;
	}
	public String getObject() {
		return object;
	}
	public void setObject(String object) {
		this.object = object;
	}
	public float getScore() {
		return score;
	}
	public void setScore(float score) {
		this.score = score;
	}
}
```

## 配置接口映射

### StudentMapper.java

```java
package mapper;

public interface StudentMapper {
	public int queryScoreByNameAndObject(String name, String object) throws Exception;
}
```

### 新建 SQL 语句映射文件 StudentMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="mapper.StudentMapper">
	<select id="queryScoreByNameAndObject" parameterType="pojo.FirstParameter"  resultType="pojo.FirstResult">
		select a.studentname, b.score, b.object
		  from student a, score b
		 where a.studentno = b.studentno
		   and a.studentname =#{studentname}
		   and b.object = #{object}
	</select>
</mapper>
```

由 SQL 语句得知这是两个表的查询语句
传入参数有 studentname, object,
传出参数有 a.studentname, b.object, b.score。

以上数据因为都是从两个表中获取，所以需要再写两个 pojo 类：FirstParameter.java 和 FirstResult.java

### FirstParameter.java

```java
package pojo;

public class FirstParameter {
	private String studentname;
	private String object;
	private String getStudentname() {
		return studentname;
	}
	public void setStudentname(String studentname) {
		this.studentname = studentname;
	}
	public String getObject() {
		return object;
	}
	public void setObject(String object) {
		this.object = object;
	}
}
```

### FirstResult.java

```java
package pojo;

public class FirstResult {
	private String studentname;
	private float score;
	private String object;
	public String getStudentname() {
		return studentname;
	}
	public void setStudentname(String studentname) {
		this.studentname = studentname;
	}
	public float getScore() {
		return score;
	}
	public void setScore(float score) {
		this.score = score;
	}
	public String getObject() {
		return object;
	}
	public void setObject(String object) {
		this.object = object;
	}
}
```
## 测试类

```java
package test;

import java.io.IOException;
import java.io.InputStream;
import java.util.HashMap;
import java.util.Map;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import pojo.FirstResult;
import pojo.FirstParameter;




public class Test {
	public static void main(String[] args) throws IOException {
		InputStream is = Resources.getResourceAsStream("SqlMapConfig.xml");
		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(is);
		SqlSession session=sqlSessionFactory.openSession();
		// 查询张三的语文成绩
		FirstParameter d=new FirstParameter();
		d.setStudentname("张三");
		d.setObject("语文");
		FirstResult q=session.selectOne("queryScoreByNameAndObject", d);
		if(q!=null){
			System.out.println(q.getStudentname()+":"+q.getScore());
		}
	}
}
```

