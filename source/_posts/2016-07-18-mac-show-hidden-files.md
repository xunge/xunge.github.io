---
layout: post
title: macOS 如何显示隐藏文件
tags:
  - mac
categories: 瞎折腾
featureImage: https://img.xungejiang.com/static/images/16-7-18/006.jpg
permalink: mac-show-hidden-files
updated: '2016-07-18 09:01:42'
date: 2016-07-18 09:01:42
---

mac 为了系统安全会将一些文件夹隐藏，避免用户误删除造成的系统崩溃，但是在安装配置文件时经常需要到隐藏目录下操作，所以这里介绍两种方法，将隐藏的文件显示出来。

<!--more-->




## 第一种方法 命令行

命令方式最简单，键入如下两行命令你就可以实现对文件的现实和隐藏功能了。

```
显示：defaults write com.apple.finder AppleShowAllFiles -bool true
隐藏：defaults write com.apple.finder AppleShowAllFiles -bool false
```

然后 **重启 Finder** !!!!很重要!!!!楼主就是试了好多次最后发现栽在这上面了

方法如图

{% qnimg 16-7-18/005.jpg %}

{% qnimg 16-7-18/002.jpg %}

## 第二种方法 Finder 中设置

在 Finder 中进入任意文件夹，按快捷键`Command + F` 调出搜索窗口，点击"种类"选项卡，在下面找到"其他"，如图所示

{% qnimg 16-7-18/003.jpg %}

在弹出的窗口里 找到"文件可见性" 选项(可通过搜索快速查找)，勾选后面的方框,点击"好"保存设置。

{% qnimg 16-7-18/004.jpg %}

当然！！仍然需要重启 Finder ！！方法同上。
