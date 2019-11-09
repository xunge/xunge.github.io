---
title: 正则表达式简介
tags:
  - 正则
categories: 知识点
thumbnail: https://img.xungejiang.com/static/images/17-5-2/regex.jpg
updated: '2017-05-02 09:01:42'
date: 2017-05-02 09:01:42
---

正则表达式是一种特殊的字符串模式，用于匹配一组字符串。本文将介绍正则表达式的简单规则。

<!--more-->



## 正则表达式通用匹配符号

正则表达式|说明|正确示例|错误示例
-|-|-|-
.|匹配任何单个符号，包括所有字符|(“..”, “a%”) – true|(“..”, “a”) – false
^xxx|在开头匹配正则xxx|(“^a.c.”, “abcd”) – true|(“^a”, “ac”) – false
xxx\\$|在结尾匹配正则xxx|(“..cd\\$”, “abcd”) – true|(“a\\$”, “aca”) – false
[abc]|能够匹配字母a,b或c|(“^[abc]d.”, “ad9”) – true|(“[ab]x”, “cx”) – false
[^abc]|当^是[]中的第一个字符时代表取反|(“[^ab][^12].”, “c3#”) – true|(“[^ab][^12]“, “c2″) – false
[a-e1-8]|匹配a到e或者1到8之间的字符|(“[a-e1-3].”, “d#”) – true|(“[a-e1-3]“, “f2″) – false
xx\\|yy|匹配正则xx或者yy|(“x.\\|y”, “xa”) – true|(“x.\\|y”, “yz”) – false

## 正则表达式元字符

正则表达式|说明
:-:|-
\d|任意数字，等同于[0-9]
\D|任意非数字，等同于[^0-9]
\s|任意空白字符，等同于[\t\n\x0B\f\r]
\S|任意非空白字符，等同于[^\s]
\w|任意英文字符，等同于[a-zA-Z_0-9]
\W|任意非英文字符，等同于[^\w]
\b|单词边界
\B|非单词边界

有两种方法可以在正则表达式中像一般字符一样使用元字符。

1. 在元字符前添加反斜杠(\)
2. 将元字符置于\Q(开始引用)和\E(结束引用)间


## 正则表达式量词

正则表达式|说明
-|-
x?|x没有出现或者只出现一次
X*|X出现0次或更多
X+|X出现1次或更多
X{n}|X正好出现n次
X{n,}|X出席n次或更多
X{n,m}|X出现至少n次但不多于m次

原文链接： [journaldev](http://www.journaldev.com/634/regular-expression-in-java-regex-example) 翻译： ImportNew.com - ImportNew读者
译文链接： [http://www.importnew.com/6810.html](http://www.importnew.com/6810.html)
[ 转载请保留原文出处、译者和译文链接。]








