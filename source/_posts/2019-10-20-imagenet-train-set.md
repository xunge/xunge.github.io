---
title: ImageNet 训练集特殊图像集合
date: 2019-10-20 09:56:07
tags:
  - imagenet
  - pytorch
categories: 经验值
---


最近运行一个 PyTorch 工程的代码，在读取数据集时没有用官方的 Dataloader，而是自己写了一个读取数据集的函数。在读取 ImageNet 2012 数据集时遇到了一些错误，在此记录一下。

<!-- more -->


## n02105855_2933.JPEG 其实是 PNG 图像

在使用 cv2 读取数据集并将其馈送到 `model(data)` 时，如果没有对data进行检验，就有可能报下面的错误

```
RuntimeError: Given groups=1, weight of size 64 3 7 7, expected input[1, 4, 224, 224] to have 3 channels, but got 4 channels instead
```

或者

```
RuntimeError: invalid argument 0: Sizes of tensors must match except in dimension 0. Got 3 and 4 in dimension 1 at /tmp/pip-req-build-58y_cjjl/aten/src/TH/generic/THTensor.cpp:689
```

这是因为训练集中有一个图片是 PNG 图片，强行将后缀名改为了 `.JPEG`，但还是保留了 PNG 图像的 4 通道（PNG图像除了 RGB 三通道外还有一个 Alpha 通道，表示图像的透明度）

这个图片就是 `n02105855_2933.JPEG`

## n04152593_17460.JPEG 其实是 HEIC 图像

这个图片在读取时没有报错，但会报 warning。

## CMYK 图像

JPEG 图像分两种，一种 RGB，一种 CMYK，下面是 CMYK 的图像列表：

```
n01739381_1309.JPEG
n02077923_14822.JPEG
n02447366_23489.JPEG
n02492035_15739.JPEG
n02747177_10752.JPEG
n03018349_4028.JPEG
n03062245_4620.JPEG
n03347037_9675.JPEG
n03467068_12171.JPEG
n03529860_11437.JPEG
n03544143_17228.JPEG
n03633091_5218.JPEG
n03710637_5125.JPEG
n03961711_5286.JPEG
n04033995_2932.JPEG
n04258138_17003.JPEG
n04264628_27969.JPEG
n04336792_7448.JPEG
n04371774_5854.JPEG
n04596742_4225.JPEG
n07583066_647.JPEG
n13037406_4650.JPEG
```

当你在读取 ImageNet 2012 训练集遇到问题时，可以先尝试使用验证集进行训练，确认不是程序问题，而是训练集问题时，可以尝试删除或替换上述图像。