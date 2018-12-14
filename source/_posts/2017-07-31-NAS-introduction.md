---
title: 群晖 NAS 简单体验
tags:
  - NAS
categories: 瞎折腾
permalink: NAS-introduction
updated: '2017-07-31 09:01:42'
date: 2017-07-31 09:01:42
---


好吧，最近家人出去玩不带我，自己在家无聊买了一个 `群晖（Synology）DS216play` ，`2017-7-29` 京东购买，下午就到了，狗东物流就是快啊。

买的是带 **两块** 希捷 **4T** 硬盘的套装，一共 `3799` 元。单买是 (2250 + 1299 * 2) = 4848 元，相当于赠了一块硬盘，还算挺合适的，当然比 618 贵 100+ 。

<!--more-->

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-04-001.jpg?imageView2/2/w/400)



NAS (Network Attached Storage) 网络附属存储，也叫网络存储器，是专门用来存储数据的服务器，家用的主要功能其实就是私有云、照片电影的存储等。在各大网盘都被封掉的时代，买一个 NAS 存放一些私有文件还是一个比较好的选择。

传说中群晖是买软件送硬件，一是说群晖性价比低，二是说群晖软件做的确实良心，各大平台都有，插件也比较全，可玩性比较高。

## 配置 群晖

`DS216play` 是双硬盘位，不支持热插拔硬盘位。个人认为双盘位在家庭中使用足够了，8T (4T * 2) 的硬盘也够使好长时间了。

把两个硬盘装好后，拧上螺丝，插上电源和网线(连路由器或交换机的 LAN 口)，按下电源键就可以开机啦~

在连接网络的电脑浏览器输入网址 [http://find.synology.com](http://find.synology.com) 进行初次配置，设置群晖账号密码等。

之后会提示你安装推荐插件，先点取消，因为我们还要更改一下 RAID 格式。

## 选择 RAID 类型

详细的 `shr` `basic` `raid0` `raid1` `raid5` `raid6` 类型介绍参照下面的链接  [http://xungejiang.com/2017/08/03/shr-raid015/](http://xungejiang.com/2017/08/03/shr-raid015/)

安装完系统后默认为 `shr` 格式，双盘位时为 raid1 模式，多了数据备份功能，但是容量只有一半，也就是说 2 块 4T 的硬盘只有 4T 的容量。由于是家庭使用，没有太重要的文件，所以没必要进行数据备份，需要把 `shr` 模式改为 `basic` 模式，这样可用容量才是 8T。

具体方法如下。

如果 NAS 里已经有一些重要的资料不想拿另一个硬盘备份，可以参照这篇博客 [如何将raid1（SHR）降级为basic](http://support-cn.synology.me/wordpress/?p=589)。

不过如果你已经把资料都备份了，推荐恢复出厂设置。方法如下：

`控制面板` -> `更新和还原` -> `重置` -> `删除所有数据`。

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-002.png?imageView2/2/w/600)

如果你已经安装过插件，不建议你选择 `删除存储空间` 进行重置，因为插件容易卸载不干净，影响后续使用。所以最好的方法还是恢复出厂设置。

变为新系统后，在 `存储空间管理员` -> `存储空间` -> `删除` -> `删除` 将系统默认的 shr 删掉。

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-001.png?imageView2/2/w/600)

之后点 `新增` -> `自定义` -> `使用所有硬盘容量的存储空间` -> `勾选第一个硬盘` -> `确定` -> `Basic` -> `否` -> `下一步` -> `应用`

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-003.png?imageView2/2/w/600)
![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-004.png?imageView2/2/w/600)
![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-005.png?imageView2/2/w/600)
![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-006.png?imageView2/2/w/600)
![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-007.png?imageView2/2/w/600)
![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-008.png?imageView2/2/w/600)
![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-009.png?imageView2/2/w/600)
![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-010.png?imageView2/2/w/600)
![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-011.png?imageView2/2/w/600)


同理，第二块硬盘重复上述操作，只是在第四步勾选第二块硬盘。

![](http://7xvx4s.com2.z0.glb.qiniucdn.com/17-08-01-012.png?imageView2/2/w/600)

## 套件中心 初体验

群晖的软件可谓是真的良心，很多插件都已经集成，可直接下载，兼容性很强。

`存储空间分析器`：可查看文件类型、重复文件等。

`Cloud Station Server`：可下载 `Cloud Station Drive` 和 `Cloud Station Backup` 两个客户端，区别是 `Drive` 是 **双向同步**，保证云端和本地一致；而 `Backup` 只有新增才会同步，删除本地云端不会删除。

`Cloud Sync`：可同步各大网盘，以 `百度网盘` 为例，配置好后，只要将文件保存至 `我的应用数据` -> `Cloud Sync` 里即可自动下载到 NAS 中，不过速度较慢

`Download Station`：远程下载，不过速度较慢。由于迅雷取消了第三方软件的远程下载，只有迅雷的下载包和小米路由器可以使用，所以群晖的远程下载也被取消，远程下东西只能用 `Download Station` 和 同步云盘。