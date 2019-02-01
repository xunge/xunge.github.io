---
title: Markdown 简明语法手册
tags:
  - Markdown
categories: 知识点
updated: '2017-02-07 09:01:42'
date: 2017-02-07 09:01:42
---


Markdown 是文本
在此，我们总结 Markdown 的优点如下：

纯文本，所以兼容性极强，可以用所有文本编辑器打开。
让你专注于文字而不是排版。
格式转换方便，Markdown 的文本你可以轻松转换为 html、电子书等。
Markdown 的标记语法有极好的可读性。

<!--more-->



## 粗体，斜体，删除线

代码如下：

```
**粗体**
*斜体*
~~删除线~~
```

显示效果：

- **粗体**
- *斜体*
- ~~删除线~~

## 分级标题

可以行首加井号表示不同级别的标题 (H1-H6)，代码如下：

```
# H1
## H2
### H3
#### H4
##### H5
###### H6
```

因为该代码会加入目录里，所以不做演示了。

## 外链接

代码如下：

```
[本人博客](http://xungejiang.com "xunge的博客")
[本人博客](http://xungejiang.com)
```

显示效果：

[本人博客](http://xungejiang.com "xunge的博客")
[本人博客](http://xungejiang.com)

链接后的 title 需要用引号括起来，可以选填，效果是鼠标放到链接上会有提示。

如果安装了 MarkdownExtended 插件的话，可以使用 `mdl` + `tab 键`

需要注意的是，使用 Markdown 方法，默认是在本网页打开新网页，如果想在新的标签页上打开链接，只能使用 `HTML` 语言实现，代码如下：

```html
<a href="http://xungejiang.com" target="_blank">本人博客</a>
```

显示效果：

<a href="http://xungejiang.com" target="_blank">本人博客</a>

## 插入图片

### 普通 markdown 语法

代码如下：

```
![小米](http://7xvx4s.com2.z0.glb.qiniucdn.com/mi.jpg "小米")
```

显示效果：

![小米](http://7xvx4s.com2.z0.glb.qiniucdn.com/mi.jpg "小米")

如果安装了 MarkdownExtended 插件的话，可以使用 `mdi` + `tab 键`

需要注意的是，使用 Markdown 方法，图片将不能调整大小，有以下两种方式可以调整大小

### HTML 语法

使用 `HTML` 语言实现，代码如下：

```
<img src="http://7xvx4s.com2.z0.glb.qiniucdn.com/mi.jpg" width="50%"/>
```

显示效果：

<img src="http://7xvx4s.com2.z0.glb.qiniucdn.com/mi.jpg" width="50%"/>

### 使用支持参数的图床

可以使用支持参数的图床，例如七牛，可参考[七牛图片基本处理](https://developer.qiniu.com/dora/api/basic-processing-images-imageview2)。

例如代码为：

```
![小米](http://7xvx4s.com2.z0.glb.qiniucdn.com/mi.jpg "小米")                     //旧方法
![小米](http://7xvx4s.com2.z0.glb.qiniucdn.com/mi.jpg?imageView2/2/w/200 "小米")  //新方法
```

显示效果：

![小米](http://7xvx4s.com2.z0.glb.qiniucdn.com/mi.jpg?imageView2/2/w/200 "小米")

`imageView2/2/w/200` 的意义为 宽度固定为200px，高度等比缩小。

## 代码块

### 行内代码

用反引号将短代码框住，代码如下：

	这是 `行内代码`

显示效果：

这是 `行内代码`

### 多行代码

多行代码有两种表示方式。

一种是用前后两个 \`\`\` 把代码包围起来，并在第一行后面标注哪种语言，即可实现代码高亮。注意 \` 不是单引号而是左上角的ESC下面~中的 \`

代码如下：

	```sql
	CREATE TABLE stu (
	    stu_no INT(20),
	    stu_name VARCHAR(20) NOT NULL,
	    stu_tel VARCHAR(15),
	    CONSTRAINT pk_stu_no PRIMARY KEY (stu_no),
	    CONSTRAINT uk_stu_tel UNIQUE KEY (stu_tel)
	);
	```

显示效果：

```sql
CREATE TABLE stu (
    stu_no INT(20),
    stu_name VARCHAR(20) NOT NULL,
    stu_tel VARCHAR(15),
    CONSTRAINT pk_stu_no PRIMARY KEY (stu_no),
    CONSTRAINT uk_stu_tel UNIQUE KEY (stu_tel)
);
```
另一种是把代码选中后按一下 tab 键，缺点是无法识别代码语言，无法高亮。

## 列表

### 无序列表

使用 *，+，- 任意一种表示无序列表，代码如下：

```
- 无序列表项 一
	+ 无序列表项 二
		* 无序列表项 三
		* 无序列表项 四
	+ 无序列表项 五
	+ 无序列表项 六
- 无序列表项 七
```

显示效果：

- 无序列表项 一
	+ 无序列表项 二
		* 无序列表项 三
		* 无序列表项 四
	+ 无序列表项 五
	+ 无序列表项 六
- 无序列表项 七

### 有序列表

代码如下：

```
1. 有序列表项 一
2. 有序列表项 二
3. 有序列表项 三
```

显示效果：

1. 有序列表项 一
2. 有序列表项 二
3. 有序列表项 三

## 引用

代码如下：

```
> 引用文字 一
```

显示效果：

> 引用文字 一

## 表格

第一行为表头，第二行分隔表头和主体部分，默认 `-` 左对齐， `:-:` 居中对齐， `-:` 右对齐，第三行开始每一行为一个表格行，代码如下：

```
这是第一列 左对齐|这是第二列 中间对齐|这是第三列 右对齐
-|:-:|-:
小姜|男|99
小宫|女|100
小刘|男|98
```

显示效果：

这是第一列 左对齐|这是第二列 中间对齐|这是第三列 右对齐
-|:-:|-:
小姜|男|99
小宫|女|100
小刘|男|98

## 分割线

三个以上的星号、减号、底线线来建立一个分隔线，效果相同，代码如下：

```
---
***
___

```

显示效果：

---
***
___


## 上下角标

`<sub>` 和 `</sub>` 中间的为下角标
`<sup>` 和 `</sup>` 中间的为上角标

```
H<sub>2</sub>O
E=mc<sup>2</sup>
```

显示效果：
H<sub>2</sub>O
E=mc<sup>2</sup>

也可以用下面介绍的 LaTex 公式，更方便。

## LaTeX 公式

### `\\(` 和 `\\)` 表示行内公式：

代码：

```
质能守恒方程可以用一个很简洁的方程式 \\(E=mc^2\\) 来表达。
```

显示效果：

质能守恒方程可以用一个很简洁的方程式 \\(E=mc^2\\) 来表达。

### `$$` 表示整行公式：

代码：

```
$$
\sum_{i=1}^n a_i=0
$$

$$
f(x_1,x_x,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2
$$

$$
\sum^{j-1}_{k=0}{\widehat{\gamma}_{kj} z_k}
$$
```

显示效果：

$$
\sum_{i=1}^n a_i=0
$$

$$
f(x_1,x_x,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2
$$

$$
\sum_{k=0}^{j-1} {\widehat{\gamma}_{kj} z_k}
$$

查看 Sublime 如何配置 LaTex 可参考 [我写的这篇文章](https://xungejiang.com/2017/03/03/markdown-mathjax/)



以上。