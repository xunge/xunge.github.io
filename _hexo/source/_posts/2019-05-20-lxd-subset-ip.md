---
title: LXD 使用 Netplan 实现在同一网络访问
tags:
  - lxd
  - lxc
  - ubuntu
  - netplan
categories: 瞎折腾
thumbnail: https://img.xungejiang.com/static/images/19-05-20/001.jpg
updated: '2019-05-20 09:01:42'
date: 2019-05-20 09:01:42
---

为了使实验室服务器可以被多人同时使用而不互相影响，实验室每一个人都拥有一个自己的 LXD 容器，但是 LXD 默认使用自己的网桥，无法在实验室的内网直接访问。

本文使用 Ubuntu 18.04 默认的网络配置工具 Netplan 管理网络，实现 LXD 容器的 IP 与实验室的 IP 域相同。

<!--more-->

## 宿主机网络配置

Ubuntu 17.10 以后默认使用 Netplan 管理网络。进入 `/etc/netplan/` 目录有一个 yaml 配置文件，下面的命令需要根据自己的 yaml 文件名称自行修改

备份配置文件：

```
sudo cp /etc/netplan/01-netcfg.yaml /etc/netplan/01-netcfg.yaml.bak
```

编辑 yaml 配置文件：

```
sudo vim /etc/netplan/01-netcfg.yaml
```

如下：

```
# This file describes the network interfaces available on your system
# For more information, see netplan(5).
network:
  version: 2
  renderer: networkd
  ethernets:
    eno1:
      dhcp4: no
      dhcp6: no
  bridges:
    br0:
      dhcp4: no
      dhcp6: no
      interfaces:
        - eno1
      addresses: [ 192.168.1.2/24 ]
      gateway4: 192.168.1.1
      nameservers:
          addresses:
              - 114.114.114.114
              - 8.8.8.8
      parameters:
          stp: false
          forward-delay: 0
```

- `addresses: [ 192.168.1.2/24 ]` 为任意网络无人占用的 IP 即可。
- `gateway4` 为网关地址。
- `eno1` 为网卡名称，可以使用 `ip a` 或 `ifconfig` 命令查看。

应用网络配置：

```
sudo netplan --debug apply
```

重启后确认网络可用：

```
sudo reboot
ip a
ping baidu.com
```

## 容器网络配置

进入容器：

```
sudo lxc exec <container> bash
```

编辑 yaml 配置文件：

```
nano /etc/netplan/50-cloud-init.yaml
```

如下：

```
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      dhcp6: no
      addresses:
        - 192.168.1.3/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 114.114.114.114
          - 8.8.8.8
```

应用网络配置：

```
netplan --debug apply
```

同样重启后检查网络连接：

```
sudo reboot
ip a
ping baidu.com
```

## 参考资料

[Lxd + Netplan + Static IP’s in same subnet HOW-TO](https://discuss.linuxcontainers.org/t/lxd-netplan-static-ips-in-same-subnet-how-to/1074)