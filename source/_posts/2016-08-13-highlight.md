---
title: wordpress 使用 highlight.js 添加语法高亮
tags:
  - wordpress
categories: 瞎折腾
featureImage: https://img.xungejiang.com/static/images/16-8-13/001.jpg
permalink: highlight
updated: '2016-07-19 09:01:42'
date: 2016-07-19 09:01:42
---


wordpress 使用 highlight.js 添加语法高亮

转载作者博客：[snovey](http://www.snovey.com/)

<!--more-->





## highlight.js 简介

wordpress 有强大的 [Crayon Syntax Highlighter](https://cn.wordpress.org/plugins/crayon-syntax-highlighter/)，因为太过强大，很多功能用不上，用这个插件会拖慢网站速度，于是找到了这个插件：[highlight.js](https://highlightjs.org/)，如果你只是想给代码添加简单的高亮而不需要添加行号、复制按钮之类的功能，那么这款插件刚好适合你。下面简单的介绍一下：
[highlight.js](https://highlightjs.org/) 是一款强大的代码高亮插件。官方给出描述如下：

>- 支持 166 种语言，有 77 种样式
>- 自动识别语言
>- 同时支持多种语言
>- 支持 node.js 平台
>- 支持各种标记
>- 兼容任何 js 框架

该项目已在 Github 开源，项目地址：[highlight.js](https://github.com/isagalaev/highlight.js)
安装的思路非常简单：

>1. 导入 CSS 文件
>2. 导入 JS 文件
>3. 加载 JS

## 导入 highlight.js

最简单粗暴的方法如下，在 `header.php` 中加入如下代码：

```html
<link rel="stylesheet" href="/path/to/styles/default.css">
<script src="/path/to/highlight.pack.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
```

注意修改路径！
当然，这个办法非常不可取，JS 应当放在 `<body>` 中而非 `<head>` 中，所以改进后的办法是将

```html
<script src="/path/to/highlight.pack.js"></script>
<script>hljs.initHighlightingOnLoad();</script>
```

移至 `footer.php` 中 `</body>` 标签之前。
为了插件化我更推荐你这样做：在 `function.php` 中添加如下代码

```javascript
function add_highlight_js(){
        wp_enqueue_style('highlightcss','/path/to/styles/default.css');
        wp_register_script('highlightjs','/path/to/highlight.pack.js'); //注册 handle
        wp_enqueue_script('highlightjs', false, array(), false, true); //放至<body>下方
    }
    add_action('wp_enqueue_scripts', 'add_highlight_js');
```

然后在 `footer.php` 中添加

```html
<script>hljs.initHighlightingOnLoad();</script>
```

设置触发函数。

## 关于安装路径


如果是下载至本地，那么在网站的 `/wp-content/plugins/` 目录下新建 `highlight` 文件夹，然后将压缩包解压至该文件夹，将上述的

```
/path/to/styles/default.css
/path/to/highlight.pack.js
```

修改为

```php
<?php echo get_site_url() . '/wp-content/plugins/highlight/default.css';?>
<?php echo get_site_url() . '/wp-content/plugins/highlight/highlight.min.js';?>
```

即可。如果想要提高网站的速度，也可以不从本地加载，转而使用第三方提供的 CDN，下面贴几个。

cdnjs

```
https://cdnjs.cloudflare.com/ajax/libs/highlight.js/9.6.0/highlight.min.js
```

yandex:

```
http://yandex.st/highlightjs/8.2/styles/default.min.css
http://yandex.st/highlightjs/8.2/highlight.min.js
```

Bootstrap

```
http://cdn.bootcss.com/highlight.js/9.6.0/styles/default.min.css
http://cdn.bootcss.com/highlight.js/9.6.0/highlight.min.js
```

注意 `cdnjs` 不提供 CSS，而 `yandex` 貌似没有 8.2 以后的版本，根据自己的情况选择 CDN 吧。如果你想自定义代码高亮，不妨从 CDN 加载 JS，从本地加载 CSS，这里不啰嗦了。

## 使用 highlight.js

`hljs.initHighlightingOnLoad()` 会寻找 `<pre><code>` 标签，所以使用 highlight.js 时应当这样写代码：

```html
<pre><code class="html">...</code></pre>
```

记得在 class 中填写语言的类型。啥？介绍时不是说可以自动识别么？即便如此，标记语言类型是一种良好的编码习惯。

参考链接：

[highlight.js](https://highlightjs.org/)
[highlight.js](https://github.com/isagalaev/highlight.js)
[如何正确引用 JavaScript 和 CSS 文件](http://blog.wpjam.com/article/how-to-include-js-and-css-in-wordpress/)
