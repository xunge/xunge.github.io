---
title: 本地配置 Jekyll
tags:
  - Jekyll
categories: 瞎折腾
permalink: jekyll-install
updated: '2017-01-28 09:01:42'
date: 2017-01-28 09:01:42
---

本文主要是为了让博客系统在本地跑起来，如果不想在本地运行，可以无视本文，但我还是强烈建议试着先在本地跑起来，没有什么问题后再推送的 GitHub 上。
<!--more-->


<img src="http://7xvx4s.com2.z0.glb.qiniucdn.com/17-03-28-001.png" width="50%">





Jekyll是一个开源的博客生成工具，类似WordPress。但与之不同的是，jekyll只生成静态网页，并不需要数据库支持。通常配合第三方评论系统使用，例如 [有言](http://www.uyan.cc/), ~~Disqus（由于众所周知的原因上不去）~~, ~~多说（已倒闭）~~。GitHub Pages 原生支持 jekyll，而且可以绑定自己的域名。

关于 [GithubPages 绑定自定义域名](http://xungejiang.com/2017/01/27/github-domain-name/) 可以参考这篇文章。

## 安装 Ruby

Jekyll是用ruby语言编写的，所以我们首先要在windows上装好ruby环境。

### 下载 [RubyInstaller](http://rubyinstaller.org/downloads/)

注意选择对应的操作系统版本为 64位 还是 32位。

### 安装 Ruby

记得要勾选 **Add Ruby executables to your PATH**，其作用是绑定ruby环境变量，另外安装目录不可以包含空格。

### 下载DevKit

与RubyInstller同一链接，页面稍下方有“DEVELOPMENT KIT”， 注意：DevKit版本要与上面的ruby版本是匹配的。

### 安装DevKit

解压DevKit完成后打开CMD窗口，回到Devkit根目录，输入：

```
ruby dk.rb init
ruby dk.rb install
```

返回的分别是

```
[INFO] found RubyInstaller v2.3.3 at C:/Ruby23-x64

Initialization complete! Please review and modify the auto-generated
'config.yml' file to ensure it contains the root directories to all
of the installed Rubies you want enhanced by the DevKit.
```

```
[INFO] Updating convenience notice gem override for 'C:/Ruby23-x64'
[INFO] Installing 'C:/Ruby23-x64/lib/ruby/site_ruby/devkit.rb'
```


## 安装 Jekyll

### 更换源

无翻墙软件，可使用国内淘宝提供的源

```
gem sources --remove https://rubygems.org/
gem sources -a https://ruby.taobao.org/
gem sources -l
```

有翻墙软件，可以使用如下源

```
gem sources --remove https://rubygems.org/
gem sources -a  http://rubygems.org/
```

### 安装 Jekyll

```
gem install jekyll
```

### 安装 paginate

```
gem install jekyll-paginate
```

## 使用jekyll

网上找个模板好看的 github pages 的博客， Clone 下来。

Clone 有两种方法

第一种是 https 方法，通过直接输入账号密码的格式提交代码；
第二种是 ssh 的方式，需要提前配置 SSH ，之后可直接 push 代码。

Git 的基本操作参考 [git介绍 github的基本配置](http://xungejiang.com/2016/07/07/github/) 这篇文章。

```
git clone https://github.com/[username]/[username].github.io.git
git clone git@github.com:[username]/[username].github.io.git
```

启动jekyll服务

```
cd xxxx.github.io.git
jekyll s
```

## 提交文章

```
cd {username.github.io}
git add .
git commit -m "提交简介"
git push origin master
```
