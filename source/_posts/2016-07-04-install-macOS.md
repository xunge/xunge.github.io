---
title: 使用 U 盘安装 mac OS 系统
tags:
  - mac
  - OS
categories: 瞎折腾
featureImage: https://img.xungejiang.com/static/images/16-7-4/001.jpg
permalink: install-macOS
updated: '2016-07-04 09:01:42'
date: 2016-07-04 09:01:42
---


安装 mac 系统有很多种方法，如网上恢复、时间机器恢复、App Store在线安装等等，但是我还是推荐使用 U 盘安装，因为不仅高效，而且还可以得到干净纯粹的系统。本文安装的 macOS 版本为 El Capitan，其他 macOS 依然适用。

<!--more-->




## 前言

博主家里有一台iMac一体机，买回来的时候就是win7（肯定是老爸要求换的），所以也一直当成windows电脑用着。然而博主家里还有台 MacBook Pro，被博主强行换成mac系统，简直不能再漂亮，再好用，所以决定将 iMac 也换成 mac 系统，再装win10，给老爸用。

然而开机长按 option（alt）键发现原来 mac 系统一直没删，这下就好办了。不过 mac 系统有点旧，还是 lion 系统，于是想给 mac 系统做一下彻底的升级。

## 制作 mac 启动 U 盘

这里网上也有很多帖子写的很好，这里也简单说一下。

首先准备一个 8G 左右的 U 盘，当然最好 3.0。然后进入 mac 系统（如果没有 mac 系统的话看看有没有同学有用苹果本的借一下。。实在没有安一个mac虚拟机也可以）。进入实用工具 -> 磁盘工具，将U盘抹掉，格式为 `OS X 扩展(日志式)` ，名字叫做 `Capitan` (最好一致，后面代码用得上)。

当然还需要 El Capitan 安装程序，可以从 App Store上 下载(推荐，保证最新版)。也可以[百度云下载](http://pan.baidu.com/s/16O0I)下载。

确保在应用程序里有 **安装OS X El Capitan**。

{% qnimg 16-7-4/003.jpg %}

之后在 `实用工具` -> `终端` 输入下面代码

```
sudo /Applications/Install\ OS\ X\ El\ Capitan.app/Contents/Resources/createinstallmedia --volume /Volumes/Capitan --applicationpath /Applications/Install\ OS\ X\ El\ Capitan.app --nointeraction
```

其中Capitan是U盘名字，如若之前没听话没改名字，这里需要把**Capitan**改成你的U盘的名字。

做好后是这样滴

{% qnimg 16-7-4/007.jpg %}

如果出现错误则重新执行一次。

这样mac启动U盘就做好啦~

## 安装前准备

在这之前最好适用 Time Machine（时间机器）备份一下。如果电脑之前安过windows的话可能要费劲一些了，如果你的windows还分了好多区的话那就更麻烦了。。非常建议将windows分成一个区 `bootcamp` (C盘)，否则无法更新mac系统。或者像博主一样彻底一点将windows删除，更新完系统再重装windows。

## 重启安装

插上U盘，重启电脑，并长按 `Option键` （alt键），进入选盘界面（如果不成功重启再试一次）

{% qnimg 16-7-4/002.jpg %}

进入后界面如下

{% qnimg 16-7-4/004.jpg %}

可以先进入 `安装 OS X`，选择 mac 系统盘，并下一步即可。

## 可能出现的问题

下面是可能会出现的问题，如果成功安装则跳过下文。

- 如果提示 `这个磁盘没有使用GUID分区表方案` 如下图的话

{% qnimg 16-7-4/005.jpg %}

则说明windows分区破坏了GUID分区，需要将windows分区删掉才能升级mac系统。

- 如果删除了windows分区仍提示 `无法在此硬盘安装更新` 等类似字样的话，则说明只能将原来mac系统盘抹掉。

方法是退出安装程序，在 `OS X 实用工具` 中选择磁盘工具，将 mac 系统盘抹掉,这样就能成功安装了（一个空电脑当然能成功安装系统）。

- 如果遇到 `不能验证这个“安装 OS X El Capitan”应用程序副本。它在下载过程中可能已遭破坏或篡改。`

{% qnimg 16-7-4/006.jpg %}

不要真的相信程序坏掉了，解决方法是：先退出安装程序，在上面菜单栏选择 `终端`，输入下面代码

```
date 122014102015.30
```

这应该是系统bug，需要改一下时间，改完之后就应该没问题了。


---

2018.10 Update

mac 系统新推出的 `macOS Mojave` 版本依然可以使用上述方法安装系统。

在终端输入代码制作安装 U 盘时可能会报 Warning，但并不影响。