---
title: Sublime 介绍以及插件推荐
tags:
  - sublime
  - plugin
categories: 瞎折腾
thumbnail: https://img.xungejiang.com/static/images/16-7-3/016.jpg
updated: '2016-07-03 09:01:42'
date: 2016-07-03 09:01:42
---


简单介绍一下刚开始使用 sublime 的配置方式和插件。


<!--more-->




## 前言

**Sublime** 可是一款程序猿必知，必用，必会的一款文本编辑器，当然配有适当的插件也可以编译 **C**, **C++**, **Java**, **Python** 等所有简单程序，并且可以写 **Markdown** 文档，博主的博客就是用 sublime 写的哦~

## Sublime 的下载

[Sublime Text 3 官网下载](https://www.sublimetext.com/3)

下载完成后字体可能太小， `Ctrl` + `=` 可以将字体调大。同理 `Ctrl` + `-` 可以调小字体。

**Sublime** 并不是免费的软件，需要70刀左右。。然而博主还是个学生，比较穷，所以只好选择破解版。。不过等将来有钱了，我一定会来买正版的，现在就当为他的产品使用量做贡献吧。

怎么破解就不说了吧（网上搜该版本的注册码）

## Package Control 的安装

既然要用 sublime 那一定是看中他的插件功能啦，然而插件的安装需要先安装 [Package Control](https://packagecontrol.io/installation)，方法如下：

`Ctrl` + `~` 或者 `View` -> `Show Console` 调出 `Console` ，并将下面代码粘贴执行。

```
import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'https://sublime.wbond.net/' + pf.replace(' ','%20')).read())
```

如果安装成功，就可以在 Preferences 菜单下看到 `Package Settings` 和 `Package Control` 两个菜单。

若不能通过以上方式成功安装，可尝试以下方式手动安装：

点击 `Preferences` -> `Browse Packages...` 菜单，进入打开的目录的上层目录（即 `Sublime Text 3` 目录），再打开 `Installed Packages` 目录

点击下载[Package Control.sublime-package](https://sublime.wbond.net/Package%20Control.sublime-package)并复制到 `Installed Packages` 目录

，之后就可以尽情的去安装插件啦~

## 常用插件

经过上面安装了 Package Control 后，我们就可以通过快捷键 `Ctrl` + `Shift` + `P` 打开 Package Control 来安装插件了。在打开的输入框中输入 install ，会根据你的输入自动提示，选择 Package Control:Install Package，如下图。

![](https://img.xungejiang.com/static/images/16-7-3/001.png)

等一会，便会又弹出一个输入框，输入你要安装插件的名字即可。

这里推荐几个必安插件：

### [ConvertToUTF8](https://packagecontrol.io/packages/ConvertToUTF8)

文件转码成 `UTF-8` ，避免中文乱码。

![](https://img.xungejiang.com/static/images/16-7-3/004.gif)

### [GBK Encoding Support](https://packagecontrol.io/packages/GBK%20Encoding%20Support)

同样是解决中文乱码问题，将 `GBK` 编码转换成 `UTF-8` 编码。

![](https://img.xungejiang.com/static/images/16-7-3/006.png)
![](https://img.xungejiang.com/static/images/16-7-3/007.png)

### [Emmet](https://packagecontrol.io/packages/Emmet)

插件可以说是使用Sublime Text进行前端开发必不可少的插件

例如输入以下代码

```
ul#jiang>li.item$*4>a{Item $}
```

便可自动生成

```html
<ul id="jiang">
    <li class="item1"><a href="">Item 1</a></li>
    <li class="item2"><a href="">Item 2</a></li>
    <li class="item3"><a href="">Item 3</a></li>
    <li class="item4"><a href="">Item 4</a></li>
</ul>
```

![](https://img.xungejiang.com/static/images/16-7-3/005.gif)

详情参考 [前端开发必备！Emmet使用手册](https://www.w3cplus.com/tools/emmet-cheat-sheet.html)，[Emmet 官方文档](https://docs.emmet.io/abbreviations/syntax/)。

### [JsFormat]()

这是一款 JS 格式化的插件，`Ctrl` + `Alt` + `F` 对 JS 进行格式化。

### [SideBarEnhancements](https://packagecontrol.io/packages/SideBarEnhancements)

是一款很实用的右键菜单增强插件，可以对左侧文件栏进行更多的操作，更多配置请点击标题。

在安装该插件前，在Sublime Text左侧FOLDERS栏中点击右键，只有三个功能。

![](https://img.xungejiang.com/static/images/16-7-3/008.png)

通过Package Control安装SideBarEnhancements插件后

![](https://img.xungejiang.com/static/images/16-7-3/009.png)

可见功能增加了不少。

### [TrailingSpaces](https://packagecontrol.io/packages/TrailingSpaces)

这款插件能高亮显示多余的空格和Tab，并一键去除，是处女座的福音。

一键删除多余空格（需配置） `Ctrl` + `Alt` + `T`

点击 `Preferences` -> `Key Bindings – User` 加上代码

```
{ "keys": ["ctrl+alt+t"], "command": "delete_trailing_spaces" }
```

![](https://img.xungejiang.com/static/images/16-7-3/010.gif)

### [Alignment](https://packagecontrol.io/packages/Alignment)

"=" 对齐

默认快捷键 `Ctrl` + `Alt` + `A` 和QQ截屏冲突，可设置其他快捷键如：`Ctrl` + `Alt` + `Shift` + `A`:

点击 `Preferences` -> `Key Bindings – User` 加上代码

```
{ "keys": ["ctrl+alt+shift+a"], "command": "alignment" }
```

先选择要对齐的文本，如下图：

![](https://img.xungejiang.com/static/images/16-7-3/011.gif)

### [SublimeCodeIntel](https://packagecontrol.io/packages/SublimeCodeIntel)

为代码补全插件，支持 JavaScript, Mason, XBL, XUL, RHTML, SCSS, Python, HTML, Ruby, Python3, XML, Sass, XSLT, Django, HTML5, Perl, CSS, Twig, Less, Smarty, Node.js, Tcl, TemplateToolkit, PHP 等多种语言。

该插件安装时间可能较长，需要耐心等待。

### [DocBlockr](https://packagecontrol.io/packages/DocBlockr)

生成优美的注释，更多配置请点击标题。

![](https://img.xungejiang.com/static/images/16-7-3/012.gif)

![](https://img.xungejiang.com/static/images/16-7-3/013.gif)

### [Color​Picker](https://packagecontrol.io/packages/ColorPicker)

调色板，默认 `Ctrl` + `Alt` + `C` ，但与 `ConvertToUTF8` 快捷键冲突。

解决方法：更改 `ConvertToUTF8` 快捷键为 `Ctrl` + `Alt` + `Shift` + `C` 。

点击 `Preferences` -> `Browse Packages...` -> `ConvertToUTF8` -> `Default (Windows).sublime-keymap`(根据你的操作系统，打开相应文件) -> 就不用说了吧~

![](https://img.xungejiang.com/static/images/16-7-3/014.png)

### [FileDiffs](https://packagecontrol.io/packages/FileDiffs)

强大的比较代码不同工具。

右键标签页，出现 `FileDiffs Menu` 或者 `Diff with Tab…` 选择对应文件比较即可。

![](https://img.xungejiang.com/static/images/16-7-3/015.gif)

### [Tag](https://packagecontrol.io/packages/Tag)

这是HTML/XML标签缩进、补全、排版和校验工具。使用方法如下图：

![](https://img.xungejiang.com/static/images/16-7-3/003.png)

暂时没有快捷键。

## Markdown 插件

### [Markdown Preview](https://packagecontrol.io/packages/Markdown%20Preview)

Markdown Preview 可以实时将 markdown 文件在浏览器上显示。

快捷键设置如下

点击 Preferences --> 选择 Key Bindings User，输入：

```
{ "keys": ["alt+m"], "command": "markdown_preview", "args": {"target": "browser", "parser":"markdown"} }
```

**Alt + M** 为在浏览器显示

**Ctrl + B** 为转换成 html 文件

**记得和之前的快捷键用逗号隔开**

### [Markdown Extended](https://packagecontrol.io/packages/Markdown%20Extended)  + [Monokai Extended](https://packagecontrol.io/packages/Monokai%20Extendedw)

我最爱用的 Markdown 主题

### [Markdown Editing](https://packagecontrol.io/packages/MarkdownEditing)

输入 "mdi + tab" 会自动插入下面的图片标记

```
![Alt text](/path/to/img.jpg "Optional title")
```

输入 "mdl + tab" 会自动生成下面的链接标记

```
[](link)
```

## Sublime 更改字体

Sublime自带的英文字体是 `Consola` ，非常好看，但是中文默认是宋体，不太协调，所以这里可以改成 `YaHeiConsola` 字体，英文是 `Consola`，中文是 `微软雅黑`。

下载字体 [YaHeiConsola](https://pan.baidu.com/s/1pKgTFsv)，右键安装。

在Menu 中点击 **Preference** -> **Setting-User**, 添加

```yaml
{
    "font_face": "YaHeiConsola",
    "font_size": 12
}
```

> 注意：参数之间用逗号隔开

## 总结

Sublime 还有很多功能，例如可以编译很多编程语言。这是博主写的 [Sublime Text 3 设置C/C++编译环境](https://xungejiang.com/2016/07/07/sublime-C/) 可以参考。