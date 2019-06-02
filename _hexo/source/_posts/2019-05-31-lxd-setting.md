---
title: 使用 LXD 搭建多人使用的 GPU 服务器
tags:
  - lxd
  - lxc
  - ubuntu
categories: 瞎折腾
date: 2019-05-31 11:17:22
updated: 2019-05-31 11:17:22
thumbnail: https://img.xungejiang.com/static/images/19-05-31/001.jpg
---

由于深度学习的火热以及深度学习对 GPU 的强烈需求，实验室购置了一台性能强悍的 GPU 服务器，供大家一起使用。然而如果所有人都对这台服务器拥有控制权是十分危险的，例如误删他人文件，弄乱他人环境等。最直观的方法就是为每一个同学开启一个虚拟机，但是硬件虚拟化造成大量的资源浪费，同时GPU并不支持常规的虚拟化。

本文采用在宿主机上创建多个 LXD 容器的软件虚拟化方法，使得资源能够更好的利用。

<!--more-->

基于上述背景提出以下需求：

- 不同用户之间不能相互影响且可以同时使用；
- 用户可以使用 ssh 方便地访问自己的“机器”；
- 用户拥有所有权限；
- 用户不被允许直接操作宿主机；
- 用户可以使用宿主机的所有资源，包括 CPU、GPU、内存、硬盘等。

本人在 Ubuntu 18.04 的宿主机上使用 LXD 容器中的 Ubuntu 18.04 系统完成了上述所有需求。

## 安装 lxd、zfs 及 bridge-utils

```
sudo snap install lxd
sudo apt install zfs bridge-utils
```

我们需要安装LXD实现虚拟容器，ZFS作为LXD的存储管理工具，bridge-utils用于搭建网桥。由于apt安装的LXD不是最新版本，这里使用snap安装工具安装LXD。

## LXD 初始化

```
sudo lxd init
```

在初始化过程中，不要创建新的网桥，ZFS设置大小要尽量大，其他设置默认即可。

## 新建容器

如果网速允许可以尝试：

```
lxc launch ubuntu:xenial yourContainerName
```

如果网速不行可以添加清华大学的镜像：

```
lxc remote add tuna-images https://mirrors.tuna.tsinghua.edu.cn/lxc-images/ --protocol=simplestreams –public
```

使用如下命令查看清华镜像的所有系统：

```
lxc image list tuna-images:
```

选择容器系统并记录第二列的 FINGERPRINT，如 ubuntu/18.04 的FINGERPRINT为 `98fa50a3b311`

然后使用如下命令创建新容器：

```
lxc launch tuna-images:98fa50a3b311 yourContainerName
```

## 配置网络环境

在实验室的其他电脑访问服务器中的 LXD 容器有两种方法。

1. 给每个用户分配一个端口，利用 iptables 把这个端口转发到对应 LXD 容器的 22 端口；
2. 使用桥接模式，每个容器的 IP 与实验室的 IP 域相同，物理外部、局域网内部的机器可以直接 ssh 容器的 IP，不需要端口号就可以登陆。

很明显，第 2 种方法比第 1 种方法好，不仅登录方便，而且每个容器的所有端口都是开放的，可以拿其他端口做一些事情，如搭建网站、开启服务等。

第 2 种方法的详细步骤参见本人博客 [LXD 使用 Netplan 实现在同一网络访问](https://xungejiang.com/2019/05/20/lxd-subset-ip/)

## 修改容器的用户名密码

由于容器默认的用户名为 `ubuntu`，我们需要在安装其他程序之前更改用户名即密码，以避免路径冲突。

修改root密码：

```
passwd root
```

修改用户ubuntu密码：

```
passwd ubuntu
```

修改ubuntu用户名：

```
vim /etc/passwd
vim /etc/shadow
vim /etc/group
```

将上述所有文件的 `ubuntu` 更改为想改的用户名。

## 容器中安装 ssh 服务

```
sudo apt-get install openssh-server
```

至此，已经可以使用实验室的任意电脑通过 ssh 直接访问容器而不需要操作宿主机。

## 安装显卡驱动

在宿主机中首先需要安装显卡驱动，使用如下命令安装推荐的显卡驱动：

```
sudo ubuntu-drivers autoinstall
```

但是这个方法安装的驱动往往不是最新的，可以去 [NVIDIA 官网](https://www.nvidia.com/Download/index.aspx?lang=cn) 下载最新驱动并安装。具体方法参考 [Ubuntu 16.04安装NVIDIA驱动](https://blog.csdn.net/CosmosHua/article/details/76644029)

使用 nvidia-smi 查看驱动版本，并在官网下载对应的驱动，以便于在容器中安装和宿主机相同版本的NVIDIA驱动。

为容器添加所有GPU：

```
lxc config device add <container> gpu gpu
```

添加指定GPU：

```
lxc config device add <container> gpu0 gpu id=0
```

在容器中安装显卡驱动：

```
sudo sh /NVIDIA-Linux-x86_64-xxx.xx.run --no-kernel-module
```

因为在容器中显卡驱动不需要安装内核文件，所以后面加上 `--no-kernel-module`。

在容器中安装好显卡驱动重启后输入 `nvidia-smi` 检查驱动是否安装成功

## 宿主机安装 lxdui 管理容器

因为 LXD 相当于 LXC 增加了 RESTful API，所以可以通过 WEB 界面管理容器。lxdui 为不错的管理界面。具体安装方法详见 [lxdui 的 github](https://github.com/AdaptiveScale/lxdui)

目前 lxdui 版本为 2.1.2，我使用有一些 bug，例如 snapshot、clone 等操作只能在容器表格中的 Actions 进行操作，进入容器后顶部的 snapshot、clone 等按钮是无效的。所以 lxdui 主要还是方便查看和管理，具体的操作还是用命令吧。

## 容器快照管理

以上所有对容器的操作基本完成，现在需要建立快照，以备将来需要恢复快照，或者可以从快照新建一个容器，这样可以避免上面的重复劳动。

创建快照：

```
lxc snapshot <container> <snapshot name>
```

恢复快照：

```
lxc restore <container> <snapshot name>
```

从快照新建一个容器 (新旧容器 MAC 地址不同)：

```
lxc copy <source container>/<snapshot name> <destination container>
```

这也是比较好的创建容器的方法。如果直接 clone 容器的话，MAC 地址等关键信息也会同样被复制。

## 容器和宿主机间复制文件：

在宿主机输入以下命令

```
lxc file push <source path> <container>/<path> # 表示从容器中复制文件到宿主机
lxc file pull <container>/<path> <target path> # 表示将宿主机的文件复制到容器
```

复制文件夹需要在最后加 `-r`

## 共享文件夹：

创建共享文件夹：

```
lxc config set <container> security.privileged true
lxc config device add <container> <device-name> disk path=/home/xxx/share source=/home/xxx/share
```

其中 `path` 为容器路径，`source` 为宿主机路径。`device-name` 随意取名字即可。

移除共享文件夹：

```
lxc config device remove <container> <device-name>
```

## CUDA 与 cuDNN

CUDA 与 cuDNN 的安装建议使用 Anaconda 安装，因为使用 Anaconda 安装 TensorFlow 或 PyTorch 都会自带 CUDA 与 cuDNN，并且据说有一定的优化。具体命令为：

```
conda create -n tf_gpu1.9 tensorflow-gpu=1.9
conda create -n pytorch pytorch=0.4
```

只要 CUDA 版本与 NVIDIA 驱动版本相对应即可，可以在 [Release Notes :: CUDA Toolkit Documentation](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html#major-components) 查找。

## 总结

至此，多人使用的 GPU 服务器就搭建完成了，当需要新建容器时，只需要完成以下几步：

1. 从快照中新建容器；
2. `lxc exec <container> bash` 进入容器；
3. 更改容器的 IP；
4. 更改容器的用户名、密码；
5. 新增共享目录；

这在以后可以编写脚本，使得新建操作更容易。