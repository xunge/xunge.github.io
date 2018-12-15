---
title: GithubPages 绑定自定义域名
tags:
  - github
categories: 瞎折腾
thumbnail: https://img.xungejiang.com/static/images/17-1-27/007.jpg
updated: '2017-01-27 09:01:42'
date: 2017-01-27 09:01:42
---


本文将着重介绍如何将 GithubPages 的博客绑定自定义域名。

<!--more-->




## 域名的好处

域名除了为了装B，给人留下深刻的印象，博主认为最大的好处是能访问的运营商更多了。

博主使用的是 **GithubPages** 作为博客的平台，带来的问题是 由于 IP 访问限制，只有使用电信运营商时才能访问，而移动运营商不能访问（联通没试过）。而绑定域名后，因为使用 DNS 解析，移动运营商的网络也可以访问了。

## 购买域名

域名购买有多种渠道。这里推荐用国外的 [Godaddy](https://sg.godaddy.com/zh/) 进行域名注册。

Godaddy 有很多的优惠码，并且可以使用支付宝付款，非常方便。博主2017年买的一年域名花了55￥。

购买域名很简单，一步步来就行，如果不能使用支付宝付款，说明使用的优惠码不支持支付宝，可以选择使用国际银行卡支付或者换个优惠码。

## 配置 DNS 解析

### 配置 DNSPOD 解析
在 Godaddy 上购买域名后，域名会自动使用 Godaddy 自己的 DNS 解析器进行解析，不过容易被墙，所以建议使用国内的 DNS 解析器。这里推荐免费的 [DNSPOD](https://www.dnspod.cn/) 进行 DNS 解析。

DNSPOD 支持 QQ 账号登录，非常方便。进入 **域名解析** -> **添加域名** ，输入你注册的域名，进入后将记录改为如图所示

|主机记录|记录类型|记录值|TTL|
|:-:|:-:|:-:|:-:|
|@|A|192.30.252.153|600|
|@|A|192.30.252.154|600|
|@|NS|f1g1ns1.dnspod.net.|86400|
|@|NS|f1g1ns2.dnspod.net.|86400|

![](https://img.xungejiang.com/static/images/17-1-27/001.jpg)

其中下面两个 NS 记录类型是不能更改的。

这里建议使用 A 记录进行解析。当然也可以使用 CNAME 解析，但是博主使用 CNAME 进行解析有时出现错误。。所以不如把解析地址指向 GitHub，让 Github 进行域名解析。

### 配置 Godaddy 解析

在 **我的账户** 中选择 **我的产品**

![](https://img.xungejiang.com/static/images/17-1-27/002.jpg)


在 **域名** 处点击 **管理**

![](https://img.xungejiang.com/static/images/17-1-27/003.jpg)


点击域名旁边的箭头，选择 **设置域名服务器**

![](https://img.xungejiang.com/static/images/17-1-27/004.jpg)


将 **标准** 改为 **定制** ，并填写 DNSPOD 的解析服务器

![](https://img.xungejiang.com/static/images/17-1-27/005.jpg)
![](https://img.xungejiang.com/static/images/17-1-27/006.jpg)


```
f1g1ns1.dnspod.net
f1g1ns2.dnspod.net
```

## Github 的 CNAME 配置

GithubPages 是支持域名绑定的，只需要在主目录里添加一个名字为 **CNAME** 文件，注意没有后缀名。文件内容为你所购买的域名，注意没有 www 前缀，例如你申请的 `xiaoming.com` ,那么 **CNAME** 的内容为 `xiaoming.com`。

这样的话当你输入 `xiaoming.github.io` 时会自动跳转到 `xiaoming.com`。

至此 GitHub 域名绑定完毕，你已经通过域名访问你的网站啦~