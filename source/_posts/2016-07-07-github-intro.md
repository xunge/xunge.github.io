---
title: github 的介绍以及基本配置
tags:
  - github
categories: 瞎折腾
featureImage: https://img.xungejiang.com/static/images/16-7-7/001.jpg
permalink: github-intro
updated: '2016-07-07 09:01:42'
date: 2016-07-07 09:01:42
---

git介绍以及github的基本配置

<!--more-->



## 配置 Git 环境

`Linux` 和 `MAC` 环境下是自带 `GIT` 的，如果使用 `Windows` 的话有如下几个解决方案。

- [GIT 官网](https://git-scm.com/download)下载
- [Cmder](http://cmder.net/)，选择 `Download full`，不仅自带 `GIT` ，而且是替代 `Windows` 自带很丑的 `cmd` 的很好选择。
- [GitHub 离线版](https://pan.baidu.com/s/1slD88nN)，GitHub 出品。

建议大家使用命令行操作，方便快捷容易理解。

## 配置 Git 用户名和邮箱

```
$ git config --global user.name "{username}"     //用户名替换{username}
$ git config --global user.email "{email}"    //邮箱替换{email}
```

## 配置 SSH

```
ssh-keygen -t rsa -C"{email}"    //邮箱替换{email}
```

一路回车到命令完成，win 系统默认在文件夹 `C:\Users\{你的用户名}\.ssh` ，该文件夹有 `id_rsa`（私钥） 和 `id_rsa.pub`（公钥） 两个文件。

将id_rsa.pub内容复制到自己的 Github 主页的 Settings -> SSH keys，添加完毕即可。


可以输入以下命令，来测试是否能够正确链接到 github

```
ssh -T git@github.com
```

若返回命令如下

```
Hi ***! You've successfully authenticated, but GitHub does not provide shell access.
```

则说明连接成功。


## 创建新的 GIT 仓库

```
::GIT 仓库初始化
git init

::添加 github 仓库 SSH 链接
git remote add origin {SSH url}

::将所有项目添加进仓库
git add .

::提交所有变化文件(还没有上传)
git commit -m "first commit"

::上传文件
git push -u origin master
```

## windows避免每次push都输入密码

如果你每次push的时候都需要输入github的用户名和密码，就会感到非常的麻烦。原因是我们push的地址使用的是https，把它改成ssh就好啦，因为我们之前已经在github上添加ssh秘钥了。这里介绍一下这个方法。

首先在git bash 输入 `$ git remote -v` 查看当前推送方法

若如下

```
origin https://github.com/someaccount/someproject.git (fetch)
origin https://github.com/someaccount/someproject.git (push)
```

则修改

```
git remote set-url origin git@github.com:someaccount/someproject.git
```

其中将https改为ssh的方式，这样就可以不用输入密码进行push了。