---
title: 激活函数总结 (Sigmoid, ReLU, Swish, Maxout)
tags:
  - 激活函数
categories:
  - 知识点
date: 2019-07-13 17:20:19
updated: 2019-07-13 17:20:19
---

神经网络中使用激活函数来加入非线性因素，提高模型的表达能力。

<!-- more -->

激活函数需要具备以下几点**性质**:

1. 连续并可导 (允许少数点上不可导) 的非线性函数。可导的激活函数可以直接利用数值优化的方法来学习网络参数。
2. 激活函数及其导函数要尽可能的简单，有利于提高网络计算效率。
3. 激活函数的导函数的值域要在一个合适的区间内，不能太大也不能太小，否则会影响训练的效率和稳定性。

## 1. Sigmoid 型激活函数

Sigmoid 型函数是指一类 S 型曲线函数，为两端饱和函数。常用的 Sigmoid 型函数有 Logistic 函数和 Tanh 函数。

### 1.1 Logistic 函数

**公式定义如下：**

$$
\sigma(x)=\frac{1}{1+\exp (-x)}
$$

**特点：**

1. 当输入值在 0 附近时，函数近似为线性函数；当输入值靠近两端时，对输入进行抑制。
1. 输入越小，越接近于 0；输入越大，越接近于 1。
1. 其输出直接可以看作是概率分布，使得神经网络可以更好地和统计学习模型进行结合。
1. 其可以看作是一个`软性门` (Soft Gate)，用来控制其它神经元输出信息的数量。

### 1.2 Tanh 函数

**公式定义如下：**

$$
\tanh (x)=\frac{\exp (x)-\exp (-x)}{\exp (x)+\exp (-x)}
$$

Tanh 函数也可以看作是放大并平移的 Logistic 函数，其值域是 (−1, 1)。

$$
\tanh (x)=2 \sigma(2 x)-1
$$

Sigmoid 两个函数对比如下图所示。如图可知，Tanh 函数的输出是 `零中心化的` (Zero-Centered)，而 Logistic 函数的输出恒大于 0。非零中心化的输出会使得其后一层的神经元的输入发生 `偏置偏移` (Bias Shift)，并进一步使得梯度下降的收敛速度变慢。

![](https://img.xungejiang.com/static/images/19-07-13/sigmoid.jpg)

## 2. ReLU 函数

**线性整流函数** (Rectified Linear Unit, ReLU)，又称**修正线性单元**，通常指代以斜坡函数及其变种为代表的非线性函数。是目前深层神经网络中经常使用的激活函数。公式定义为：

$$
\begin{aligned} \operatorname{ReLU}(x) &=\left\{\begin{array}{ll}{x} & {x \geq 0} \\ {0} & {x<0}\end{array}\right.\\ &=\max (0, x) \end{aligned}
$$

**优点：**

1. 采用ReLU的神经元只需要进行加、乘和比较的操作，计算上更加高效。
1. Sigmoid型激活函数会导致一个非稀疏的神经网络，而 ReLU 却具有很好的稀疏性，大约 50% 的神经元会处于激活状态。
3. 相比于 Sigmoid 型函数的两端饱和，ReLU 函数为左饱和函数，且在 $x > 0$ 时导数为 1，在一定程度上缓解了神经网络的梯度消失问题，加速梯度下降的收敛速度。

**缺点：**

1. ReLU 函数的输出是非零中心化的，给后一层的神经网络引入偏置偏移，会影响梯度下降的效率。
1. ReLU 神经元在训练时比较容易“死亡”。在训练时，如果参数在一次不恰当的更新后，第一个隐藏层中的某个 ReLU 神经元在所有的训练数据上都不能被激活，那么这个神经元自身参数的梯度永远都会是 0，在以后的训练过程中永远不能被激活。

为了避免上述情况，有几种 ReLU 的变种也会被广泛使用。

### 2.1 Leaky ReLU

**带泄露的ReLU** (Leaky ReLU) 在输入 $x < 0$ 时，保持一个很小的梯度 $\gamma$。这样当神经元非激活时也能有一个非零的梯度可以更新参数，避免永远不能被激活。带泄露的ReLU的定义如下:

$$
\begin{aligned} \text { LeakyReLU(x)} &=\left\{\begin{array}{ll}{x} & { x>0} \\ {\gamma x} & { x \leq 0}\end{array}\right.\\ &=\max (0, x)+\gamma \min (0, x) \end{aligned}
$$

其中 $\gamma$ 是一个很小的常数，比如 0.01。当 $\gamma < 1$ 时，带泄露的 ReLU 也可以写为

$$
\text { Leaky ReLU }(x)=\max (x, \gamma x)
$$

相当于是一个比较简单的 maxout 单元。

### 2.2 PReLU

**带参数的 ReLU** (Parametric ReLU, PReLU) 引入一个可学习的参数，不同神经元可以有不同的参数。对于第 $i$ 个神经元，其 PReLU 的定义为：

$$
\begin{aligned} \operatorname{PReLU}_{i}(x) &=\left\{\begin{array}{ll}{x} & { x>0} \\ {\gamma_{i} x} & { x \leq 0}\end{array}\right.\\ &=\max (0, x)+\gamma_{i} \min (0, x) \end{aligned}
$$

其中 $\gamma_{i}$ 为 $x \leq 0$ 时函数的斜率。因此，PReLU 是非饱和函数。如果 $\gamma_{i}=0$，那么 PReLU 就退化为 ReLU。如果 $\gamma_{i}$ 为一个很小的常数，则 PReLU 可以看作带泄露的 ReLU。PReLU 可以允许不同神经元具有不同的参数，也可以一组神经元共享一个参数。

### 2.3 EReLU

**指数线性单元** (Exponential Linear Unit, ELU)是一个近似的零中心化的非线性函数，其定义为：

$$
\begin{aligned} \operatorname{ELU}(x) &=\left\{\begin{array}{ll}{x} & { x>0} \\ {\gamma(\exp (x)-1)} & { x \leq 0}\end{array}\right.\\ &=\max (0, x)+\min (0, \gamma(\exp (x)-1)) \end{aligned}
$$

其中 $\gamma \geq 0$ 是一个超参数，决定 $x \leq 0$ 时的饱和曲线，并调整输出均值在 0 附近。

### 2.4 Softplus

**Softplus 函数**可以看作是 rectifier 函数的平滑版本，其定义为：

$$
\operatorname{Softplus}(x)=\log (1+\exp (x))
$$

Softplus 函数其导数刚好是 Logistic 函数。Softplus 函数虽然也有具有单侧抑制、宽兴奋边界的特性，却没有稀疏激活性。

下图给出了 ReLU、Leaky ReLU、ELU 以及 Softplus 函数的示例：

![](http://img.xungejiang.com/static/images/19-07-13/relu.jpg)

## 参考

- [激活函数(ReLU, Swish, Maxout)](https://www.cnblogs.com/makefile/p/activation-function.html)
- [神经网络与深度学习](https://nndl.github.io/)