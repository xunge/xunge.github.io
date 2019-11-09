---
title: macOS 如何安装 tomcat
tags:
  - mac
  - tomcat
categories: 瞎折腾
thumbnail: https://img.xungejiang.com/static/images/16-7-18/001.jpg
updated: '2016-07-18 09:01:42'
date: 2016-07-18 09:01:42
---


Tomcat 服务器是一个免费的开放源代码的 Web 应用服务器，属于轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试 JSP 程序的首选。

博主是在学习 JavaWeb 的时候接触到 tomcat 的，这里介绍一下在 mac 系统安装 tomcat 的过程

<!--more-->


## 下载 tomcat

首先到 [tomcat 官网](http://tomcat.apache.org) 下载 tomcat ，版本既然是新版兼容旧版当然是越新越好啦。

选择 `zip` 或者 `tar.gz` 下载即可。下载后解压，更名为 `tomcat` ，并复制到 `/Library` (就是finder中的资源库)。当时博主将文件夹重命名为 tomcat 后居然带后缀名 `tomcat.M9` 。。文件夹居然带后缀名！！后缀名居然删不掉！！也不知道那个 `.M9` 是咋出来的。。在表面看是看不到的啊！！

## 修改授权

tomcat中的几个运行服务程序都是以*.sh结尾的，在运行之前需要授权。打开终端输入如下命令:

```
sudo chmod 755 /Library/tomcat/bin/*.sh
```

其中 tomcat 为你的文件夹名。(博主当时很无奈的将tomcat换成了tomcat.M9)

回车出现要输入密码：请输入本机账户密码

## 启动 tomcat 服务

先使用 cd 命令进入tomcat的bin目录,命令如下:

cd /Library/tomcat/bin/

启动服务命令:

```
sudo sh startup.sh
```

启动成功,会出现如下结果:

```
Using CATALINA_BASE:   /ProgramFile/tomcat
Using CATALINA_HOME:   /ProgramFile/tomcat
Using CATALINA_TMPDIR: /ProgramFile/tomcat/temp
Using JRE_HOME:        /System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/Home
Using CLASSPATH:       /ProgramFile/tomcat/bin/bootstrap.jar:/ProgramFile/tomcat/bin/tomcat-juli.jar
Tomcat started.
```

如果出现如上结果，说明tomcat启动成功。

这个时候输入 http://localhost:8080/ 应该就可以访问了。