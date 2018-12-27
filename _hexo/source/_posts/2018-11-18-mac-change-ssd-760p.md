---
title: 14 年 MacBook Pro 升级 Intel 760p NVME SSD
tags:
  - mac
  - ssd
categories: 瞎折腾
thumbnail: https://img.xungejiang.com/static/images/18-11-18/001.jpg
updated: '2018-11-18 09:01:42'
date: 2018-11-18 09:01:42
---


本教程适用于 13/14/15 年 MacBook Pro 升级非官方硬盘

<!--more-->

由于 MacBook Pro 的硬盘接口是自己设计的，所以更换硬盘有两种选择：

- 选择 苹果 自家接口的固态硬盘。优点：兼容性好；缺点：贵，速度慢
- 选择 M.2 接口且兼容的固态硬盘 + 转接卡。优点：便宜，速度快，兼容性也很好；缺点：自己动手有风险（很小），失去保修，不好出二手

本文将介绍使用第三方 M.2 接口的固态硬盘和转接卡升级 MacBook Pro 硬盘的方案

## 之前的准备

### SSD 与转接卡的选择

SSD 主要是根据广大网友的前车之鉴，目前比较推荐的有三星 SM951，Intel 760P等。博主选择的是 Intel 760P，因为 SM951 目前很难买到新款，基本上都是拆机的二手货，没有质保，而 Intel 760P 有 5 年保修，价格也差不多。

转接卡在淘宝搜索 `苹果SSD转接卡 2014 mac book`，博主买的转接卡如下图所示。

![](https://img.xungejiang.com/static/images/18-11-18/002.jpg)

### 制作 macOS 系统盘

因为在 macOS 10.13 High Sierra 版本后系统支持了 NVME 硬盘协议，所以我们安装的 macOS 版本必须是10.13之后的系统。本文选择的是 macOS 10.14 Mojave 版本，具体制作 macOS系统盘的教程可以参考 [本人博客](https://xungejiang.com/2016/07/03/install-macOS)

### TimeMachine 备份系统

硬盘有价，数据无价！

拆机时还需要 T5 的螺丝刀，这里建议购买 `米家wiha螺丝刀`

## 更换过程

拆机等详细步骤可以参考 [IFIXIT 更换 SSD 教程](https://www.ifixit.com/Guide/MacBook+Pro+13-Inch+Retina+Display+Mid+2014+SSD+Replacement/27849)

拆机过程要注意上面两颗螺丝和其他八颗螺丝的长度是不一样的，重新装机的时候要注意。

![](https://img.xungejiang.com/static/images/18-11-18/004.jpg)

后机盖和机身有两个卡扣固定，装机要确定卡扣卡紧再上螺丝。

![](https://img.xungejiang.com/static/images/18-11-18/005.jpg)

更换完 SSD 是这样的

![](https://img.xungejiang.com/static/images/18-11-18/003.jpg)

注意一定要将硬盘接头用力往里塞紧，并保证固定螺丝是可以拧上固定的状态。


## 重装系统

插上刚才制作的 macOS 系统盘，按住 `option` 键开机，选择 install macOS 进入下面界面，选择磁盘工具，继续

![](https://img.xungejiang.com/static/images/18-11-18/006.jpg)

将新安装的 SSD 抹掉，格式为 `Mac OS 扩展（日志式）`，方案为 `GUID 分区图`，如下图所示

![](https://img.xungejiang.com/static/images/18-11-18/007.jpg)

若没有检测到新硬盘，则可能是硬盘没有插紧，或者硬盘不是全新的硬盘留有分区，只需要在 Windows 系统下删除卷即可

之后关闭磁盘工具，进入第二项 `安装 macOS` 即可成功安装。

新旧硬盘测速图如下（都是刚装完系统就测试的）：

![](https://img.xungejiang.com/static/images/18-11-18/008.jpg)

## 休眠无法唤醒的解决办法

如果将电脑扣盖休眠 8 小时后（或者电脑休眠至没电关机）再开机发现无法唤醒电脑（电脑屏幕亮，但无反应），那么你可能需要继续往下阅读

Mac 电脑默认模式下休眠时间超过 8 小时后会将内存中的资料存储在硬盘中并将内存断电，以达到更好的省电目的。可能因为转接卡或硬盘不兼容将资料从硬盘导入内存造成无法唤醒的现象。

### 方法1：重置 Mac 笔记本电脑上的 SMC 和 NVRAM：

以下来自苹果官网：

1.如何重置 Mac 笔记本电脑上的 SMC：

如果电池不可拆卸：
选取苹果菜单 >“关机”。
等 Mac 关机后，按下内建键盘左侧的 Shift-Control-Option，然后同时按下电源按钮。按住这些按键和电源按钮 10 秒钟。
如果您的 MacBook Pro 带有触控 ID，则触控 ID 按钮也是电源按钮。
松开所有按键。
再次按下电源按钮以开启 Mac。

2.如何重置 NVRAM

将 Mac 关机，然后开机并立即同时按住以下四个按键：Option、Command、P 和 R。您可以在大约 20 秒后松开这些按键，在此

期间您的 Mac 可能看似在重新启动。

在发出启动声的 Mac 电脑上，您可以在两次启动声之后松开这些按键。
在 iMac Pro 上，您可以在 Apple 标志第二次出现并消失后松开这些按键。 
如果您的 Mac 使用了固件密码，这个组合键将不起任何作用或导致您的 Mac 从 macOS 恢复功能启动。要重置 NVRAM，请先关闭固件密码。

在您的 Mac 完成启动后，您可能需要打开“系统偏好设置”并调整已重置的任何设置，例如音量、显示屏分辨率、启动磁盘选择或时区。

### 方法2：更改电源管理模式

如果上述方法依旧存在休眠无法唤醒的情况，那就只能使用下述命令通过更改 hibernatemode，使电脑不进入内存断电的休眠模式。

```bash
# 永不休眠
sudo pmset -a hibernatemode 0 standby 0 autopoweroff 0
# 恢复默认
sudo pmset -a hibernatemode 3 standby 1 autopoweroff 1
```

详细命令参考博客 [mac下pmset的使用方法](https://www.cnblogs.com/zhengran/p/4802582.html)

虽然更改后可能会略微增加耗电，但是开盖唤醒速度也会加快。需要注意的是更改模式后在电脑快要没电的时候需要关机，否则可能导致数据的丢失。