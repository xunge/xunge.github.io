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
sudo apt install zfsutils-linux bridge-utils
```

我们需要安装LXD实现虚拟容器，ZFS作为LXD的存储管理工具，bridge-utils用于搭建网桥。由于apt安装的LXD不是最新版本，这里使用snap安装工具安装LXD。

## LXD 初始化

```
sudo lxd init
```

在初始化过程中，不要创建新的网桥，ZFS设置大小要尽量大，其他设置默认即可。详情如下：

```
Would you like to use LXD clustering? (yes/no) [default=no]:
Do you want to configure a new storage pool? (yes/no) [default=yes]:
Name of the new storage pool [default=default]: lxd
Name of the storage backend to use (btrfs, ceph, dir, lvm, zfs) [default=zfs]:
Create a new ZFS pool? (yes/no) [default=yes]:
Would you like to use an existing block device? (yes/no) [default=no]:
Size in GB of the new loop device (1GB minimum) [default=100GB]: 800
Would you like to connect to a MAAS server? (yes/no) [default=no]:
Would you like to create a new local network bridge? (yes/no) [default=yes]: no
Would you like to configure LXD to use an existing bridge or host interface? (yes/no) [default=no]: yes
Name of the existing bridge or host interface: br0
Would you like LXD to be available over the network? (yes/no) [default=no]:
Would you like stale cached images to be updated automatically? (yes/no) [default=yes]
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]:
```

## 新建容器

如果网速允许可以尝试：

```
sudo lxc launch ubuntu:xenial yourContainerName
```

如果网速不行可以添加清华大学的镜像：

```
sudo lxc remote add tuna-images https://mirrors.tuna.tsinghua.edu.cn/lxc-images/ --protocol=simplestreams –public
```

使用如下命令查看清华镜像的所有系统：

```
sudo lxc image list tuna-images:
```

选择容器系统并记录第二列的 FINGERPRINT，如 ubuntu/18.04 的FINGERPRINT为 `0023c4e9dc6e`

然后使用如下命令创建新容器：

```
sudo lxc launch tuna-images:0023c4e9dc6e yourContainerName
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

修改用户名：

```
usermod -l <newname> -d /home/<newname> -m <oldname>
groupmod -n <newgroup> <oldgroup> 
```

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
sudo lxc config device add <container> gpu gpu
```

添加指定GPU：

```
sudo lxc config device add <container> gpu0 gpu id=0
```

在容器中安装显卡驱动：

```
sudo sh ./NVIDIA-Linux-x86_64-xxx.xx.run --no-kernel-module
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
sudo lxc snapshot <container> <snapshot name>
```

恢复快照：

```
sudo lxc restore <container> <snapshot name>
```

从快照新建一个容器 (新旧容器 MAC 地址不同)：

```
sudo lxc copy <source container>/<snapshot name> <destination container>
```

这也是比较好的创建容器的方法。如果直接 clone 容器的话，MAC 地址等关键信息也会同样被复制。

## 容器和宿主机间复制文件：

在宿主机输入以下命令

```
sudo lxc file push <source path> <container>/<path> # 表示从容器中复制文件到宿主机
sudo lxc file pull <container>/<path> <target path> # 表示将宿主机的文件复制到容器
```

复制文件夹需要在最后加 `-r`

## 共享文件夹：

创建共享文件夹：

```
sudo lxc config set <container> security.privileged true
sudo lxc config device add <container> <device-name> disk path=/home/xxx/share source=/home/xxx/share
```

其中 `path` 为容器路径，`source` 为宿主机路径。`device-name` 随意取名字即可。

移除共享文件夹：

```
sudo lxc config device remove <container> <device-name>
```

## CUDA 与 cuDNN

CUDA 与 cuDNN 的安装建议使用 Anaconda 安装，因为使用 Anaconda 安装 TensorFlow 或 PyTorch 都会自带 CUDA 与 cuDNN，并且据说有一定的优化。具体命令为：

```
conda create -n tf_gpu1.9 tensorflow-gpu=1.9
conda create -n pytorch pytorch=0.4
```

只要 CUDA 版本与 NVIDIA 驱动版本相对应即可，可以在 [Release Notes :: CUDA Toolkit Documentation](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html#major-components) 查找。

## 安装远程桌面 (2019.08.11 更新)

参考文档：[How to Connect to a Ubuntu 18.04 Server via Remote Desktop Connection using xRDP](https://draculaservers.com/tutorials/ubuntu-18-xrdp/)

### 1. 安装 xRDP

xRDP 是一款非常不错的远程桌面软件，且全平台支持。安装 xRDP 最好在干净的系统上安装。

- Windows 可以直接使用微软的远程桌面连接。
- Mac 可以使用 `Microsoft Remote Desktop` mac 版，App Store 搜不到，这里放上 [下载链接](https://go.microsoft.com/fwlink/?linkid=868963)。
- Linux 可使用 `FreeRDP`、`Rdesktop` 等开源软件。

```
sudo apt-get update
sudo apt-get install xrdp
```

### 2. 安装桌面环境

Ubuntu 有很多桌面环境，例如 XFCE, Lubuntu, Xubuntu 和 MATE 等。这里使用 XFCE 为例：

```
sudo apt-get install xfce4
```

现在就可以使用远程桌面软件，通过 IP 即可访问。

## 在 LXD / LXC 中使用 Docker 容器 (2019.09.01 更新)

参考文档：[docker run hello-world still fails, permission denied - stackoverflow](https://stackoverflow.com/questions/39557576/docker-run-hello-world-still-fails-permission-denied/44152580)

在 LXD 环境中依然可以使用 Docker，但是需要更改一些配置，否则在 pull 镜像时会有 `permission denied` 错误，如下：

```
OCI runtime create failed: container_linux.go:345: starting container process caused "process_linux.go:430: container init caused \"rootfs_linux.go:58: mounting \\\"proc\\\" to rootfs \\\"/var/lib/docker/vfs/dir/c5cedb213621362913c6d950eec507ba91e04f2a933cd6d309f1c74a92c346ec\\\" at \\\"/proc\\\" caused \\\"permission denied\\\"\"": unknown
```

我们只需要在宿主机对容器进行以下配置即可：

```
sudo lxc config set <container> security.nesting true
sudo lxc config set <container> security.privileged true
```

## RuntimeError: cuda runtime error (30) (2019.10.10 更新)

最近实验室新配的一台四路 RTX2080Ti 服务器遇到了一些奇怪的问题，就是容器环境配好后重启宿主机，再使用容器里的环境时会找不到 CUDA，报下面的错误：

```
RuntimeError: cuda runtime error (30) : unknown error at /tmp/pip-req-build-58y_cjjl/aten/src/THC/THCGeneral.cpp:50
```

网上其他人报这个错误可能是因为 CUDA 没有安装，但我使用的是 Anaconda 自动安装的 CUDA 和 cuDNN，容器和宿主机都可以运行 `nvidia-smi` 命令查看显卡驱动版本，并且在重启前是没有错误的，为什么重启后就报错了呢？为此我甚至重装系统，但这个问题依然存在。。。

目前临时的解决办法是：

1. 重启宿主机后，需要使用宿主机的 Python 环境运行一次使用 CUDA 的程序；
2. 重启所有容器。

这样容器里的 CUDA 就可以找到了。这可能是 LXC 的配置问题，如果有人遇到相同问题有更好的解决方案希望可以告知，万分感谢~

## 宿主机重启后找不到显卡驱动(2020.6.2 更新)

重启宿主机后，显卡驱动掉了，所有用到显卡的容器都无法启动，输入`nvidia-smi`报错：

```
NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver. Make sure that the latest NVIDIA driver is installed and running
```

原因是系统更新了内核，导致安装显卡驱动时的内核与现有内核不一致，解决办法参考[此解决办法 ](https://www.jianshu.com/p/3cedce05a481)

```
sudo apt-get install dkms # DKMS，Dynamic Kernel Module Support，可以帮我们维护内核外的这些驱动程序，在内核版本变动之后可以自动重新生成新的模块。
sudo dkms install -m nvidia -v 430.50
```

DKMS全称是Dynamic Kernel Module Support，它可以帮我们维护内核外的这些驱动程序，在内核版本变动之后可以自动重新生成新的模块。

430.50是安装驱动的版本，可进入/usr/src查看nvidia-430.50的文件夹获取版本号

重启后输入nvidia-smi就应该正常输出了。如果还是不好使，可以重启后重新运行上述命令再重启。

## 容器内 torch.cuda.is_available() 是 false (2020.6.2 更新)

宿主机重启后，容器的pytorch只能使用CPU，无法使用GPU，体现为在pytorch环境下输入下述命令返回`false`，但是宿主机输入下述命令为`true`：

```
python -c 'import torch; print(torch.cuda.is_available())'
```

检查容器的显卡驱动和cuda等都是正常的。原因是重启后之前的容器参数失效，重新输入下述命令即可：

```
sudo lxc config set yanyihan security.privileged true
```

## 总结

至此，多人使用的 GPU 服务器就搭建完成了，当需要新建容器时，只需要完成以下几步：

1. 从快照中新建容器；
2. `lxc exec <container> bash` 进入容器；
3. 更改容器的 IP；
4. 更改容器的用户名、密码；
5. 新增共享目录；

这在以后可以编写脚本，使得新建操作更容易。