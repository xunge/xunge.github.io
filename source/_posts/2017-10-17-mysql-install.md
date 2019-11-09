---
title: Windows 下 MySQL 绿色版安装详细教程
tags:
  - MySQL
categories: 瞎折腾
updated: '2017-10-17 09:01:42'
date: 2017-10-17 09:01:42
---


MySQL 绿色版安装详细操作步骤。

MySQL 绿色版优点是安装时间短，可在一部电脑兼容多个版本的 MySQL。

<!--more-->




## 1. 下载。

下载地址：[http://downloads.mysql.com/archives/get/file/mysql-5.7.11-winx64.zip](http://downloads.mysql.com/archives/get/file/mysql-5.7.11-winx64.zip)

可以复制链接使用迅雷下载，速度较快。

## 2. 解压 MySQL 压缩包

解压到指定目录，我的是 “C:\MySQL\mysql-5.7.11-winx64”

## 3. 修改配置文件

将解压目录中的 `my-default.ini` 文件重命名为 `my.ini`，并将内容替换为以下即可

```ini
[mysqld]
# 注意：路径是反斜线，也可以改为两个正斜线，还可以加上双引号
# 设置mysql的安装目录
basedir = C:\MySQL\mysql-5.7.11-winx64
# 设置 mysql 数据库的数据的存放目录，必须是 data
datadir = C:\MySQL\mysql-5.7.11-winx64\data
# mysql端口
port = 3306

sql_mode = NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

# 服务端编码格式
character_set_server = utf8

# 不加这句话可能报错
innodb_flush_method = normal
```

## 4. 安装MySQL服务 

以管理员身份运行 cmd

```
:: 进入 `C:\MySQL\mysql-5.7.11-winx64\bin` 目录下，
cd C:\MySQL\mysql-5.7.11-winx64\bin

:: 安装 MySQL 服务
mysqld -install
:: 显示 “Service successfully installed.” 即成功

:: 初始化 MySQL (若安装目录有 data 文件夹则删除)
mysqld --initialize

:: 启动 MySQL 服务
net start mysql
:: 显示 “MySQL 服务正在启动 .”
:: 显示 “MySQL 服务已经启动成功。”
:: 若启动失败，在任务管理器中找到 “mysqld.exe” 进程，并删除
```
## 5. 更改默认密码

打开 MySQL 安装目录，打开 data 目录，有一个 `.err` 后缀名的文件，用编辑器打开

如果每一行都是 `[Warning]`，没有 `[Error]`，就说明安装正确，并且最后一行应该如下

```
[Note] A temporary password is generated for root@localhost: oK-R(foa>4by
```

后面 12 个字符为默认生成初始密码，复制

打开 cmd ，输入以下命令

```
mysql -u root -p
:: 显示 “Enter password:” 后粘贴密码
```

若出现 “Welcome to the MySQL monitor.  Commands end with ; or \g. ...” 则说明密码正确

若出现 “ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)” 则说明密码错误，编辑 MySQL 配置文件 `my.ini` ，在 `[mysqld]` 这个条目下加入 `skip-grant-tables`，保存退出后重启 MySQL

密码正确后更改默认密码

```
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'newPassword';
```

`newPassword` 更改为新密码

## 6. 卸载 MySQL 服务

进入 `C:\MySQL\mysql-5.7.11-winx64\bin` 目录下，输入

```
mysqld -remove
```

或者

```
sc delete mysql
```

执行卸载服务。