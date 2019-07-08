---
title: 搭建本地 Anaconda 镜像
tags:
  - anaconda
categories:
  - 经验值
date: 2019-07-06 19:54:04
updated: 2019-07-06 19:54:04
---


Anaconda是一个免费开源的Python等语言的发行版本，致力于简化包管理和部署，可以大大提高环境搭建效率。

然而Anaconda国外源在国内下载速度较慢，虽然国内有清华源可以大大提高下载速度（2019年4月清华源曾因版权原因关闭，但在5月已重新开放），但是肯定没有搭建一个本地源速度快。本文将详细介绍如何将Anaconda镜像安装在本地，以供本机以及局域网内的其他电脑访问。

<!-- more -->

## 下载所有镜像文件到本地

搭建本地镜像肯定需要将所有镜像文件下载到本地。

这里感谢清华开源下载镜像文件的Python[代码](https://github.com/tuna/tunasync-scripts/blob/master/anaconda.py)，这里进行了一定的修改，代码如下：

<script src="https://gist.github.com/xunge/de60d28b5878768437148deaa52dd9d5.js"></script>

可以看到代码中 repos 只选择了 `main` 和 `free`，arches 选择了 `linux-64` 和 `win-64`，当然也可以选择同步注释代码中的更多系统和版本。博主下载了这些文件共189.1 GB（2019年6月），也就是说只需要占用不到 200 GB 的磁盘空间，无需下载即可使用Anaconda安装Python包，还是很实用的。

## 建立索引

下载的文件会在 `pkgs` 根目录下，我们需要运行以下命令

```
conda index pkgs/*
```

运行需要较长时间，运行完成后会在 `free` 和 `main` 文件夹内生成 `noarch` 文件夹。

## 搭建 http 文件服务器

为了使局域网内的用户都可访问本地Anaconda镜像，我们首先搭建一个本地http服务器，参考这篇 [博客](https://www.jianshu.com/p/e1a6219167cf)

在 ubuntu 系统下运行下面命令

```
sudo apt install apache2
```

apache2 的配置文件是 `/etc/apache2/apache2.conf`。

服务器默认的访问路径在 `/var/www/html` 目录下。

创建软链接，例如我们的镜像 pkgs 文件夹在 `/home/ubuntu/mirror/anaconda/pkgs` ，在 `/var/www/html` 目录下通过命令 `ln -s /home/ubuntu/mirror/anaconda/pkgs/ anaconda/pkgs` 创建一个软连接。就可以通过 `http://192.168.1.10/anaconda/pkgs` 访问到文件目录。

## 使用本地镜像

通过以下命令设置Anaconda的镜像路径：

```
conda config --add channels http://192.168.1.10/anaconda/pkgs/free/
conda config --add channels http://192.168.1.10/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```

至此，本地镜像的配置完成，我们可以离线安装Anaconda管理包了，速度不是一般的快。