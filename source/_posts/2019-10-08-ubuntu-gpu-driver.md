---
title: 超详细! Ubuntu 18.04 安装 NVIDIA 显卡驱动
tags:
  - ubuntu
  - nvidia
categories: 瞎折腾
date: 2019-10-08 19:24:25
updated: 2019-10-08 19:24:25
---

最近给实验室服务器安装系统，多次因为显卡驱动的问题而崩溃。。在此整理一下显卡驱动的安装。

<!-- more -->

## 查看显卡型号

不知道显卡型号的可以通过此命令查看，但可能有的新显卡无法识别。

```
lspci | grep VGA
```

## 下载显卡驱动程序

在 [NVIDIA 官网](https://www.nvidia.com/Download/index.aspx?lang=cn) 或 [GeForce 官网](https://www.geforce.com/drivers) 下载所需的显卡驱动程序。

需要注意的是显卡驱动需要和 CUDA 版本对应，而 CUDA 版本又要和 PyTorch 或 TensorFlow 的版本对应，所以原则上是越新的版本越好，因为可以支持更多版本的深度学习框架。

## 禁用 nouveau 驱动

1.使用下述命令可以查看 nouveau 驱动是否运行：

```
lsmod | grep nouveau
```

若出现下述结果：

```
nouveau              1863680  9
video                  49152  1 nouveau
ttm                   102400  1 nouveau
mxm_wmi                16384  1 nouveau
drm_kms_helper        180224  1 nouveau
drm                   479232  12 drm_kms_helper,ttm,nouveau
i2c_algo_bit           16384  2 igb,nouveau
wmi                    28672  4 intel_wmi_thunderbolt,wmi_bmof,mxm_wmi,nouveau
```

说明 nouveau 驱动正在运行。

2.运行下述命令禁用该驱动：

```
sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
```

检查命令是否正确：

```
cat /etc/modprobe.d/blacklist-nvidia-nouveau.conf
```

若出现下述结果说明命令正确：

```
blacklist nouveau
options nouveau modeset=0
```

3.更新设置并重启：

```
sudo update-initramfs -u
sudo reboot
```

4.重启后重新输入下述命令：

```
lsmod | grep nouveau
```

若没有任何输出说明禁用 nouveau 驱动成功

## 安装 NVIDIA 显卡驱动

1.安装依赖：

```
sudo apt install gcc g++ make
```

2.登录时按 `ctrl + alt + F2` 进入命令行并使用用户名密码登录，并输入 `sudo telinit 3` 打开一个新的 TTY1 界面。如果是 SSH 远程连接，则不需要做上述步骤。

3.安装驱动：

```
sudo bash ./NVIDIA-Linux-x86_64-418.56.run
```

并按下述选项选择：

![](https://img.xungejiang.com/static/images/19-10-09/nvidia01.png)

![](https://img.xungejiang.com/static/images/19-10-09/nvidia02.png)

![](https://img.xungejiang.com/static/images/19-10-09/nvidia03.png)

![](https://img.xungejiang.com/static/images/19-10-09/nvidia04.png)

![](https://img.xungejiang.com/static/images/19-10-09/nvidia05.png)

![](https://img.xungejiang.com/static/images/19-10-09/nvidia06.png)

4.安装成功后输入 `nvidia-smi`，若有类似下述输出证明显卡安装成功：

```
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.56       Driver Version: 418.56       CUDA Version: 10.1     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce RTX 208...  Off  | 00000000:19:00.0 Off |                  N/A |
| 52%   57C    P0    59W / 250W |      0MiB / 10989MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   1  GeForce RTX 208...  Off  | 00000000:1A:00.0 Off |                  N/A |
| 73%   70C    P0    73W / 250W |      0MiB / 10989MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+
|   2  GeForce RTX 208...  Off  | 00000000:67:00.0 Off |                  N/A |
| 79%   71C    P0    86W / 250W |      0MiB / 10989MiB |      1%      Default |
+-------------------------------+----------------------+----------------------+
|   3  GeForce RTX 208...  Off  | 00000000:68:00.0 Off |                  N/A |
| 44%   71C    P0     1W / 250W |      0MiB / 10986MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+
```

