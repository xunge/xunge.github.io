---
title: 远程开机_网络唤醒设置方法 (WOL, Wake on Lan) 
tags:
  - WOL
categories: 瞎折腾
updated: '2017-12-09 09:01:42'
date: 2017-12-09 09:01:42
---

最近在实验室想控制家里的电脑。控制很容易， TeamViewer 就好啦。但是白天家里没人，没人帮我开电脑，于是找到了 WOL 这种方法。

<!--more-->




## 设置主板 BIOS

需要在 BIOS 中进行更改。我的是微星 BIOS，操作如下

`高级` -> `换型事件设置` -> 将 `PCIE设备唤醒` 和 `网络唤醒` 设置为 `允许` (Enable)

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-12-09-007.jpg)

其他 BIOS 也类似，因为网卡也属于 PCIE 设备，所以 PCIE设备唤醒 也需要打开。


## 设置网卡

在设备驱动管理器中，找到 `网络适配器` ，在第一个驱动 `右键` -> `属性`

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-12-09-001.png)

在 `高级` 菜单中的属性找到 `唤醒魔包` (Wake on Magic Packet) 设置为 `启用`

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-12-09-002.png)

在 `电源管理` 中 `勾选` `允许此设备唤醒计算机`

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-12-09-003.png)

## 配置路由器 DDNS (动态 DNS) 服务

由于 IPV4 地址紧张，运营商宽带都是使用的动态 IP 地址，这就需要 `动态 DNS 服务` 进行穿透局域网。

我家的路由器是 `网件 NETGEAR R7800` 所以这里使用 NETGEAR 的 DDNS 服务，其他路由器基本也有自己的 DDNS 服务，大家可以自己选择。

首先登录路由器控制界面，一般是浏览器输入 192.168.0.1 / 192.168.1.1 / 10.0.0.1 等进入。

找到 `DDNS`  或者 `动态 DNS` ，注册 DDNS 服务商。网件提供三个 DDNS 服务商，我选择的是 `www.No-IP.com`。

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-12-09-004.png)

找到 `端口映射/端口触发`，在 `端口映射` 中 `添加自定义服务`。

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-12-09-005.png)

服务名随便填，协议：TCP/UDP，`外部端口组` 和 `内部端口组` 一致即可，`内部 IP 地址`映射到家里电脑的 IP。

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-12-09-010.png)

这样，你就可以使用 WOL 软件发送一个数据包唤醒家里的电脑了。

有一个网站就可以使用 [https://www.depicus.com/wake-on-lan/woli](https://www.depicus.com/wake-on-lan/woli)。

该网站还提供了 windows, mac OS, Android, iOS 等不同平台的应用，有需要的可以自行下载。

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-12-09-006.png)

mac 地址可以在控制台输入 `ipconfig /all` 获取；

IP 地址填 `域名即可`；

子网掩码为 `255.255.255.255`；

端口号为之前设的外部和内部端口号。
