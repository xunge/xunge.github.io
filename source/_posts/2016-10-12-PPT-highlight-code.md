---
title: PPT 中插入高亮代码的踩坑历程
tags:
  - PPT
categories: 瞎折腾
featureImage: https://img.xungejiang.com/static/images/16-10-12/001.jpg
permalink: PPT-highlight-code
updated: '2016-10-12 09:01:42'
date: 2016-10-12 09:01:42
---


转载作者：[snovey](http://www.snovey.com/)

如果你是一名小白，又梦想有朝一日成为一代大 PPT 工程师，或许你会需要这篇文章。

<!--more-->





## PPT 中插入高亮代码的踩坑历程

如何在 PPT 中插入高亮的代码？少量代码大可以手调，但是当代码多起来就力不从心了。一个显然的办法是用 HTML 对代码高亮，然后粘贴过去，HTML 高亮的方法有很多，一搜一大堆，但是 HTML 格式的代码粘贴到 Word 是高亮的，但是粘贴到 PPT（即使是从 Word 粘贴过去）都会出现问题，至少我这里会出现问题。

怎么办呢？

查了一下，微软自家的产品内部通用 [RTF (Rich Text Format)](https://www.wikiwand.com/zh/RTF) 格式，接下来就是如何得到 RTF 格式的高亮代码了。

我手头用的 Sublime，有一款插件叫做 Highlight，选中代码，右键 -> Copy as RTF，然后粘贴到 PPT 就好了。然后发现代码高亮有坑（诸如左右括号不一样颜色之类的 bug）。

Notepad++ 也有这个功能，下载最新的 Notepad++ 7.0 版本，安装插件，连个 plugin manager 都没有，手动安装出错，遂退回到 6.9 版本，然后 Copy as RTF 粘贴到 PPT，结果连背景色都带上了，RTF 那个语法，简直了，看着就想吐，别说改了，卒。

最后我选择了 [Pygments](http://pygments.org/)，安装：

```
pip install Pygments
```

执行：

```
pygmentize -f rtf -O style=paraiso-dark -l c -o code.rtf code.c
```
粘贴到 PPT，OK。

结束了 PPT 中代码高亮的噩梦。

参考：
[How can I embed programming source code in Powerpoint slide and keep code highlighting?](https://superuser.com/questions/85948/how-can-i-embed-programming-source-code-in-powerpoint-slide-and-keep-code-highli)
[Pygments Docs](http://pygments.org/docs/cmdline/)
[How to add syntax-highlighted code to PowerPoint slides (Mac OS)](https://gist.github.com/ept/4475995)
