---
title: AttributeError:'NoneType' object has no attribute 'clone'
date: 2019-10-24 16:15:28
tags:
- pytorch
categories: 经验值
---

在使用 PyTorch 计算 Tensor 的梯度时遇到了这个问题。

Debug 查看是因为 Tensor 的 `is_leaf` 是 `False`，说明无法求梯度

解决办法是 `Tensor.detach()`

就可以求梯度了