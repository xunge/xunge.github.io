---
title: ICT 实验室深度学习服务器使用说明
date: 2019-10-10 15:55:36
tags:
  - lxc
  - lxd
  - ubuntu
categories: 经验值
---

实验室新到的服务器已经配置完成。为了满足实验室所有同学的实验需求，同时最大化服务器的利用率，故为每位同学分配一个 LXD 容器，每个容器一个内网 IP。本文将详细介绍服务器的使用方式。

<!-- more -->

## 服务器配置介绍

|类型|型号|数量|
-|-|-
GPU|	技嘉RTX2080Ti TURBO 11G|	4
CPU|	i9-9820X 10核/20线程|	1
CPU散热|	美商海盗船 H150i PRO|	1
主板|	技嘉 X299-WU8|	1
机箱|	先马掠食者V1|	1
内存|	美商海盗船 复仇者LPX DDR4 3000 16GB|	8
电源|	振华 额定2000W LEADEX P 2000电源|	1
PCIE固态硬盘|	三星 1TB SSD固态硬盘 970 EVO|	1
SATA固态硬盘|	三星 1TB SSD固态硬盘 860 EVO|	1

## 为什么选择 LXD

LXD 就是一个提供了 REST API 的 LXC 容器管理器。
LXD 最主要的目标就是使用 Linux 容器而不是硬件虚拟化向用户提供一种接近虚拟机的使用体验。

其优势是

- 容器中的系统与宿主机使用同一个内核，性能损耗小；
- 容器可以使用宿主机的所有计算资源；
- 容器重启速度达到秒级；
- 轻量级隔离，在隔离的同时还提供共享机制，以实现容器与宿主机的资源共享。

使用 LXD，相当于每个人都拥有一台独立的服务器，运行环境互相隔离，例如可以安装不同的 CUDA 版本或 cuDNN 版本。（容器的显卡驱动必须和宿主机显卡驱动版本号相同，故不能改变）

目前每一个容器都已经安装好了显卡驱动，并分配了内网 IP，可以直接使用 SSH 进行远程登录。主目录有一个 `share` 共享目录，用于存放公用数据集、安装包、模型等共享资源。

## 连接 LXD 容器

目前使用 [LXDUI](https://github.com/AdaptiveScale/lxdui) 统一管理容器，地址为 [http://192.168.100.230:15151](http://192.168.100.230:15151) 进入后界面如下，可以对自己的容器进行 IP 查询以及快照管理。

![lxdui](https://img.xungejiang.com/static/images/19-10-09/lxdui.png)

找到自己的 IP 后就可以使用 SSH 工具进行远程连接，默认用户名为你的姓氏，例如登录 IP 为 `192.168.100.111`，名为 `zhangsan`，则输入下述命令进行连接：

```
ssh zhang@192.168.100.111
```

登录成功后可更改用户名和密码：

```
# 更改用户名
usermod -l <newUsername> -d /home/<newUsername> -m <oldUsername>
groupmod -n <newUsername> <oldUsername>

# 更改密码
passwd <newUsername>
```

## 安装 Python 环境

这里介绍使用 Anaconda 进行 Python 环境的配置。

1.输入下述命令安装 Anaconda（不要加 sudo）：

```
bash ~/share/install/Anaconda3-2019.07-Linux-x86_64.sh
```

其中最后一步 Init 为 yes，这样会在 ~/.bashrc 文件的最后添加初始化信息。

2.运行下述命令更新 .bashrc

```
source ~/.bashrc
```

更新后命令行前出现 `(bash)` 说明安装成功。

3.更换 Anaconda 源

由于 Anaconda 服务器在国外，换国内源可以加快下载速度。我在实验室搭建了 Anaconda 的本地镜像，本地 Anaconda 镜像的搭建可参考本人博客 [搭建本地 Anaconda 镜像](https://xungejiang.com/2019/07/06/local-anaconda-mirror/)。输入下述命令更换为实验室镜像（更新可能不及时）：

```
conda config --add channels http://192.168.100.188/pkgs/free/
conda config --add channels http://192.168.100.188/pkgs/main/
conda config --set show_channel_urls yes
```

也可以使用清华源，参考[https://mirror.tuna.tsinghua.edu.cn/help/anaconda/](https://mirror.tuna.tsinghua.edu.cn/help/anaconda/)

4.创建 Anaconda 环境

例如，我们想创建名为 “pytorch” 的 PyTorch 环境，则可以运行下述命令：

```
conda create -n "pytorch" pytorch
```

这样将安装 PyTorch 的最新版本。若想安装某个特定的版本，可以在后面加 `=1.0`。

注意，使用 Anaconda 安装深度学习框架如 PyTorch 或 TensorFlow 时会自动安装 CUDA 和 cuDNN，并且会对深度学习框架进行优化，如果没有特殊需求则不需要再安装 CUDA 和 cuDNN。当然也可以安装（如编译安装 apex）。

输入下述命令进入刚才创建的 PyTorch 环境：

```
conda activate pytorch
```

之后可以使用 `conda install xxx` 或 `pip install xxx` 安装 Python 包。

## 使用 PyCharm IDE 远程连接 Python 环境

PyCharm IDE 必须是专业版 (Professional)，如果是社区版 (Community) 需到 [官网](https://www.jetbrains.com/pycharm/download/) 下载专业版。

学生可通过咱学校的 edu 邮箱免费激活，[激活教程在此](https://blog.csdn.net/qq_36667170/article/details/79905198)

PyCharm 远程调试的教程有很多，随便找了一个 [使用PyCharm进行远程开发和调试](https://www.xncoding.com/2016/05/26/python/pycharm-remote.html)

## 硬件监控

在训练时，我们需要实时监控硬件的变化，如 GPU 的显存，占用率等。使用 `nvidia-smi` 命令可查看显存和占用率等使用状态，如下图所示。

![nvidia_smi](https://img.xungejiang.com/static/images/19-10-09/nvidia-smi.png)

一般来说，显存和占用率越高，训练速度越快。

使用下述命令可以每 0.1 秒刷新一次界面：

```
watch -n 0.1 nvidia-smi
```

使用下述网址可通过 NetData 监控硬件信息：

http://192.168.100.230:19999/#menu_nv_submenu_Load;theme=slate;help=true

效果如图：

![netdata](https://img.xungejiang.com/static/images/19-10-09/netdata.png)