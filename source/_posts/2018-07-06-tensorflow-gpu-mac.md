---
title: 我是如何在黑苹果中编译安装 TensorFlow-GPU 1.8
tags:
  - mac
  - tensorflow
  - gpu
categories: 瞎折腾
featureImage: https://img.xungejiang.com/static/images/18-7-6/001.jpg
permalink: tensorflow-gpu-mac
updated: '2018-07-06 09:01:42'
date: 2018-07-06 09:01:42
---

之前一直使用 Ubuntu Linux 系统作为 TensorFlow 机器学习的服务器，但是相对于 macOS 来说，无论是界面美化还是应用覆盖都是远远强于 Ubuntu 的，所以计划安装一个黑苹果作为 TensorFlow 的服务器

但是因为 TensorFlow 在 1.2 版本后不再支持 macOS 的 GPU 版本，只能通过编译源代码进行安装，过程较为繁杂，所以在此记录

<!-- more -->


首先确定 Mac 显卡是 NVIDIA 显卡，且compute capabilities >= 3.0，[点击这里](https://developer.nvidia.com/cuda-gpus) 查看你的显卡型号是否支持




## 环境概览

软件 | 版本号
---- | ----
macOS High Sierra | 10.13.4
TensorFlow | 1.8
python | 3.6.4
NVIDIA Web-Drivers | 387.10.10.10.30.106
CUDA-Drivers | 387.178
CUDA Toolkit | 9.1
cuDNN | 7.0.5
bazel | 0.10.0
Xcode | 8.3.2
Command Line Tools for Xcode | 8.3.2

## 环境搭建

### 安装 Homebrew

在终端输入下面命令安装 Homebrew

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

### 安装 coreutils，llvm，OpenMP

```
brew install coreutils llvm cliutils/apple/libomp
```

### 安装 Python 依赖

建议使用 Anaconda 包管理和 Virtualenv 虚拟环境等安装 Python

```
pip install six numpy wheel
```

### 安装 bazel

下载 [0.10.0](https://github.com/bazelbuild/bazel/releases) 版本中的 `bazel-0.10.0-installer-darwin-x86_64.sh` 文件

需要注意，这里必须是 0.10.0 版本，新或旧都能导致编译失败

在下载目录打开终端，输入下面命令进行安装

```
chmod +x bazel-0.10.0-installer-darwin-x86_64.sh
./bazel-0.10.0-installer-darwin-x86_64.sh
```

### 降级 Xcode 到 8.3.2

下载 `Xcode 8.3.2` 和 `Command Line Tools for Xcode 8.3.2`，Xcode 9 需要降级，因为编译 TensorFlow 只能使用 Xcode 8，官网下载需要登录苹果账号，[官网下载链接](https://developer.apple.com/download/more/)，按名称排列即可快速找到。

- `Xcode8.3.2.xip` (4.49GB) 下载后解压，重命名为 `Xcode8.3.2` 并复制到 `应用程序` 即可
- `CommandLineToolsforXcode8.3.2.dmg` (166.1MB) 下载后安装即可

使用下面的命令在终端激活 Xcode 8.3.2

```
sudo xcode-select -s /Applications/Xcode8.3.2.app
```

换回 Xcode 9 可以用

```
sudo xcode-select -s /Applications/Xcode.app
```

### NVIDIA

#### (1) 安装 NVIDIA Web-Drivers

下载 `NVIDIA Web-Drivers` 驱动，根据不同的 Mac 系统进行下载，[点击这里](https://www.tonymacx86.com/nvidia-drivers/) 下载，支持 macOS 10.13.4 的版本为 387.10.10.10.30.106

- `WebDriver-387.10.10.10.30.106.pkg` (63.9MB) 下载后安装即可

#### (2) 安装 CUDA-Drivers

下载 `CUDA-Drivers` 驱动，支持 CUDA 9.1 的版本号为 387.178，[官网](https://www.nvidia.cn/object/macosx-cuda-387.178-driver-cn.html) 下载、 [百度云](https://pan.baidu.com/s/1GyoCyuh-hhJtEgFC0XS0IQ) 下载

- `cudadriver_387.178_macos.dmg` (39.9MB) 下载后安装即可

#### (3) 安装 CUDA Toolkit 9.1

下载 `CUDA Toolkit 9.1`，[官网](https://developer.nvidia.com/cuda-91-download-archive?target_os=MacOSX&target_arch=x86_64&target_version=1013&target_type=dmglocal) 下载和 [百度云](https://pan.baidu.com/s/1zPU_2aC6_uK3P2j7v-RtEw) 下载

- `cuda_9.1.128_mac.dmg` (1.53GB) 下载后安装即可

配置 CUDA 环境，编辑 `~/.bash_profile` 文件，如果安装了zsh则编辑 `~/.zshrc` 文件，打开终端：

```
open -e .bash_profile
```

然后在弹出的文件中添加：

```
export CUDA_HOME=/usr/local/cuda
export DYLD_LIBRARY_PATH=/usr/local/cuda/lib:/usr/local/cuda/extras/CUPTI/lib
export LD_LIBRARY_PATH=$DYLD_LIBRARY_PATH
export PATH=$PATH:$DYLD_LIBRARY_PATH
```

执行命令重启bash_profile

```
. ~/.bash_profile
```

检测CUDA能否正常运行：

```
cd /usr/local/cuda/samples
sudo make -C 1_Utilities/deviceQuery
./bin/x86_64/darwin/release/deviceQuery
```

第一次编译时可能需要同意苹果协议，按照要求填 `agree` 即可

最终结果为 `Result = PASS` 则安装正确。

#### (4) 安装 cuDNN 7.0.5

下载 `cuDNN 7.0.5`，该版本支持 CUDA 9.1 ，官网下载时需要登录 NVIDIA 账号，[官网](https://developer.nvidia.com/rdp/cudnn-archive) 下载、 [百度云](https://pan.baidu.com/s/1wY5A75FzXbNVmf0ZAIwvkg) 下载

- `cudnn-9.1-osx-x64-v7-ga.tgz` (340.3MB) 下载后解压，切换到解压缩的 `cuda` 目录，输入以下命令

```
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib/libcudnn_static.a /usr/local/cuda/lib
sudo cp cuda/lib/libcudnn.7.dylib /usr/local/cuda/lib
sudo ln -s /usr/local/cuda/lib/libcudnn.7.dylib /usr/local/cuda/lib/libcudnn.dylib
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib/libcudnn*
```

## 编译准备

### 拉取 TensorFlow 源码 release 1.8 分支

```
git clone https://github.com/tensorflow/tensorflow -b r1.8
cd tensorflow
```

### 修改代码，使其与 macOS 兼容

#### 替换掉以下三个文件的 align(sizeof(T))

```
cd tensorflow
sed -i -e "s/ __align__(sizeof(T))//g" tensorflow/core/kernels/concat_lib_gpu_impl.cu.cc
sed -i -e "s/ __align__(sizeof(T))//g" tensorflow/core/kernels/depthwise_conv_op_gpu.cu.cc
sed -i -e "s/ __align__(sizeof(T))//g" tensorflow/core/kernels/split_lib_gpu.cu.cc
```

#### 解决找不到 'protobuf.bzl' 的问题

我还遇到了以下错误

```
ERROR: /Users/xunge/Desktop/tensorflow/tensorflow/tools/pip_package/BUILD:166:1: error loading package 'tensorflow': Encountered error while reading extension file 'protobuf.bzl': no such package '@protobuf_archive//': java.io.IOException: thread interrupted and referenced by '//tensorflow/tools/pip_package:build_pip_package'
```

[解决办法](https://github.com/tensorflow/tensorflow/issues/12979) 如下：

```
sed -i '\@https://github.com/google/protobuf/archive/0b059a3d8a8f8aa40dde7bea55edca4ec5dfea66.tar.gz@d' tensorflow/workspace.bzl
```

#### 添加依赖头文件 nccl.h (如编译1.7不用做此步骤)

下载 [nccl.h](https://github.com/NVIDIA/nccl/blob/master/src/nccl.h)，放在 `third_party/nccl` 文件夹内

#### 修改 tensorflow/workspace.bzl 文件

```
tf_http_archive(
    name = "protobuf_archive",
    urls = [
        "https://mirror.bazel.build/github.com/google/protobuf/archive/396336eb961b75f03b25824fe86cf6490fb75e3a.tar.gz",
        "https://github.com/google/protobuf/archive/396336eb961b75f03b25824fe86cf6490fb75e3a.tar.gz",
    ],
    sha256 = "846d907acf472ae233ec0882ef3a2d24edbbe834b80c305e867ac65a1f2c59e3",
    strip_prefix = "protobuf-396336eb961b75f03b25824fe86cf6490fb75e3a",
)
```

搜索如上替换为如下

```
tf_http_archive(
    name = "protobuf_archive",
    urls = [
        "https://mirror.bazel.build/github.com/dtrebbien/protobuf/archive/50f552646ba1de79e07562b41f3999fe036b4fd0.tar.gz",
        "https://github.com/dtrebbien/protobuf/archive/50f552646ba1de79e07562b41f3999fe036b4fd0.tar.gz",
    ],
    sha256 = "eb16b33431b91fe8cee479575cee8de202f3626aaf00d9bf1783c6e62b4ffbc7",
    strip_prefix = "protobuf-50f552646ba1de79e07562b41f3999fe036b4fd0",
)
```

修复 third_party/gpus/cuda/BUILD.tpl 文件 -lgomp 报错

```
linkopts = ["-lgomp"],
```

搜索如上，注释掉

```
# linkopts = ["-lgomp"],
```

## 开始编译

### 编译配置

在 TensorFlow 目录下输入以下命令进行命令配置

```
./configure
```

配置文件如下

```
You have bazel 0.10 installed.
Please specify the location of python. [Default is /Users/user/.pyenv/versions/tensorflow-gpu/bin/python]: 


Found possible Python library paths:
  /Users/user/.pyenv/versions/tensorflow-gpu/lib/python3.6/site-packages
Please input the desired Python library path to use.  Default is [/Users/user/.pyenv/versions/tensorflow-gpu/lib/python3.6/site-packages]

Do you wish to build TensorFlow with Google Cloud Platform support? [Y/n]: n
No Google Cloud Platform support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Hadoop File System support? [Y/n]: n
No Hadoop File System support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Amazon S3 File System support? [Y/n]: n
No Amazon S3 File System support will be enabled for TensorFlow.

Do you wish to build TensorFlow with Apache Kafka Platform support? [y/N]: n
No Apache Kafka Platform support will be enabled for TensorFlow.

Do you wish to build TensorFlow with XLA JIT support? [y/N]: n
No XLA JIT support will be enabled for TensorFlow.

Do you wish to build TensorFlow with GDR support? [y/N]: n
No GDR support will be enabled for TensorFlow.

Do you wish to build TensorFlow with VERBS support? [y/N]: n
No VERBS support will be enabled for TensorFlow.

Do you wish to build TensorFlow with OpenCL SYCL support? [y/N]: n
No OpenCL SYCL support will be enabled for TensorFlow.

Do you wish to build TensorFlow with CUDA support? [y/N]: y
CUDA support will be enabled for TensorFlow.

Please specify the CUDA SDK version you want to use, e.g. 7.0. [Leave empty to default to CUDA 9.0]: 9.1


Please specify the location where CUDA 9.1 toolkit is installed. Refer to README.md for more details. [Default is /usr/local/cuda]: 


Please specify the cuDNN version you want to use. [Leave empty to default to cuDNN 7.0]: 


Please specify the location where cuDNN 7 library is installed. Refer to README.md for more details. [Default is /usr/local/cuda]:


Please specify a list of comma-separated Cuda compute capabilities you want to build with.
You can find the compute capability of your device at: https://developer.nvidia.com/cuda-gpus.
Please note that each additional compute capability significantly increases your build time and binary size. [Default is: 3.5,5.2]6.1


Do you want to use clang as CUDA compiler? [y/N]: n
nvcc will be used as CUDA compiler.

Please specify which gcc should be used by nvcc as the host compiler. [Default is /usr/bin/gcc]: 


Do you wish to build TensorFlow with MPI support? [y/N]: n
No MPI support will be enabled for TensorFlow.

Please specify optimization flags to use during compilation when bazel option "--config=opt" is specified [Default is -march=native]: 


Would you like to interactively configure ./WORKSPACE for Android builds? [y/N]: 
Not configuring the WORKSPACE for Android builds.

Preconfigured Bazel build configs. You can use any of the below by adding "--config=<>" to your build command. See tools/bazel.rc for more details.
	--config=mkl         	# Build with MKL support.
	--config=monolithic  	# Config for mostly static monolithic build.
Configuration finished
```

### 编译

```
bazel clean --expunge
bazel build --config=cuda --config=opt --cxxopt="-D_GLIBCXX_USE_CXX11_ABI=0" --action_env PATH --action_env LD_LIBRARY_PATH --action_env DYLD_LIBRARY_PATH //tensorflow/tools/pip_package:build_pip_package
```
如果看到

```
INFO: Build completed successfully, 9160 total actions
```

就说明编译成功

### 创建wheel文件并安装

```
bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg
cd ~
sudo pip install /tmp/tensorflow_pkg/tensorflow-1.8-cp36-cp36m-macosx_10_13_x86_64.whl
```

本人编译完成后的文件为 `tensorflow-1.8.0-cp36-cp36m-macosx_10_7_x86_64.whl` [百度云下载](https://pan.baidu.com/s/14o0uIOutmW866ftbf8nvuA)

最后提供本人 Z270 + i7-7700k 的黑苹果 [EFI](https://pan.baidu.com/s/1ttgZ1cralJrTYkZS5BmwnQ)

参考文章：

- [【tensorflow】macOS 10.13.4 编译 GPU 版本的 TensorFlow 1.8](https://www.shifeng1993.com/2018/05/26/tensorflow_gpu_macos/)
