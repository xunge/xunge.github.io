---
title: GCC 编译多平台二进制程序（x64, ARM, MIPS）
date: 2021-08-01 15:48:29
tags:
- x64
- arm
- mips
categories: 知识点

---

GCC  在本平台编译比较简单，但是如果在不同平台上进行编译就需要使用交叉编译的技术，虽然命令很简单，但是网上资料还是比较匮乏，踩了不少的坑，现总结如下。

<!-- more -->

## 准备工作

我们在 Ubuntu20.04 操作系统下进行操作。需要使用 GCC 编译器，使用命令 `gcc -v` 检查编译器是否可用，若没有使用下述命令进行安装：

```shell
sudo apt update
sudo apt install build-essential
```

我们使用 binutils 这个开源C++项目作为例子，下载链接如下 [https://ftp.gnu.org/gnu/binutils/](https://ftp.gnu.org/gnu/binutils/)，下载 ` binutils-2.37.tar.gz` 到服务器目标路径 `~/project/open_source` 下。

进入目标路径：

```shell
cd ~/project/open_source
```

使用以下命令进行解压：

```shell
tar -zxvf binutils-2.37.tar.gz
```

创建编译路径并进入：

```shell
mkdir binutils-build
cd binutils-build
```

## 编译 x64 平台

因为服务器是x64的系统，所以默认编译的二进制也是x64，使用以下命令创建 Makefile 文件：

```shell
../binutils-2.37/configure CC=gcc CFLAGS=-O0 CPPFLAGS=-O0
```

上述为优化选项 O0 的编译结果，如果想改成其他优化选项，更改 CFLAGS 和 CPPFLAGS 里面的参数即可。

使用make命令进行编译：

```shell
make
```

编译成功后，在 `binutils` 文件夹下可以看到编译好的二进制文件，使用 `file` 命令可以看到其中 addr2line 二进制文件的详细信息：

```shell
file binutils/addr2line
# binutils/addr2line: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=fc36de2a595215ee0f8dba42cf2fa628699e2610, for GNU/Linux 3.2.0, not stripped
```

可知该二进制文件为 x86-64 架构的 64-bit 程序。

## 编译 MIPS 平台

首先使用下述命令删除 `binutils-build` 文件夹所有文件：

```shell
rm -rf *
```

使用下述命令安装MIPS交叉编译选项

```shell
sudo apt-get update
sudo apt-get install gcc-mips-linux-gnu
```

使用下述命令生成MIPS平台的 Makefile 文件：

```shell
../binutils-2.37/configure CFLAGS=-O0 CPPFLAGS=-O0 --host=mips-linux-gnu
```

其中 --host 表示交叉编译中的目标架构。同样，更改优化选项可直接将O0改为O1等。

使用make命令进行编译：

```shell
make
```

编译成功后，在 `binutils` 文件夹下可以看到编译好的二进制文件，使用 `file` 命令可以看到其中 addr2line 二进制文件的详细信息：

```shell
file binutils/addr2line
# binutils/addr2line: ELF 32-bit MSB executable, MIPS, MIPS32 rel2 version 1 (SYSV), dynamically linked, interpreter /lib/ld.so.1, BuildID[sha1]=53aa3d29d13350381e9c81b776308e64da46f06d, for GNU/Linux 3.2.0, not stripped
```

可知该二进制文件为 MIPS 架构的 32-bit 程序。

## 编译 ARM 平台

首先使用下述命令删除 `binutils-build` 文件夹所有文件：

```shell
rm -rf *
```

使用下述命令安装MIPS交叉编译选项

```shell
sudo apt-get update
sudo apt-get install gcc-arm-linux-gnueabi
```

使用下述命令生成MIPS平台的 Makefile 文件：

```shell
../binutils-2.37/configure CFLAGS=-O0 CPPFLAGS=-O0 --host=arm-linux-gnueabi
```

其中 --host 表示交叉编译中的目标架构。同样，更改优化选项可直接将O0改为O1等。

使用make命令进行编译：

```shell
make
```

编译成功后，在 `binutils` 文件夹下可以看到编译好的二进制文件，使用 `file` 命令可以看到其中 addr2line 二进制文件的详细信息：

```shell
file binutils/addr2line
# binutils/addr2line: ELF 32-bit LSB executable, ARM, EABI5 version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.3, BuildID[sha1]=f3c359a16a4390cd2f8a1ac13365b49ce9a7b5ef, for GNU/Linux 3.2.0, not stripped
```

可知该二进制文件为 ARM 架构的 32-bit 程序。

