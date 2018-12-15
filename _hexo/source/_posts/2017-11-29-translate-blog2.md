---
title: 【翻译】 Is attacking machine learning easier than defending it?
tags:
  - translate
  - cleverhans
categories: 论文向
updated: '2017-11-29 09:01:42'
date: 2017-11-29 09:01:42
---


原文：[Is attacking machine learning easier than defending it?](http://www.cleverhans.io/security/privacy/ml/2017/02/15/why-attacking-machine-learning-is-easier-than-defending-it.html)

译文：[攻击机器学习比防御更容易吗?](http://xungejiang.com/2017/11/29/translate-blog2/)

原文写作日期：2017年3月15日
译文写作日期：2017年11月29日

本文为[cleverhans-blog](http://www.cleverhans.io/)的第二篇博客，作者为 Ian Goodfellow 和 Nicolas Papernot，主要讲解 **对抗性训练** 和 **防御性蒸馏** 两种防御方法之间的优势与不足。译者水平有限，存在错误，还望指出。

转载请注明出处！

<!--more-->






在我们的[第一篇文章](http://www.cleverhans.io/security/privacy/ml/2016/12/15/breaking-things-is-easy.html)中，我们提出了几种攻击者可以打破当前机器学习系统的方式，比如通过毒化学习算法使用的数据[BNL12]，或者制作对抗性样本迫使模型做出错误的预测[SZS13]。在本文中，我们将以对抗性样本为例说明为什么攻击机器学习似乎比防御更容易。换句话说，我们将详细介绍为什么我们还没有完全有效的防御对抗性样本的一些原因，并推测我们是否能够进行防御。

对抗性样本是机器学习模型的输入，它是由攻击者设计，用来欺骗模型产生不正确的输出。例如，我们给一个熊猫图片添加一个经过计算的小扰动，使图像被认为是一个高可信度的长臂猿[GSS14]：

![](http://cleverhans.io/assets/adversarial-example.png) 

到目前为止，设计出这种欺骗模型的方法要比设计出不能欺骗模型的方法容易得多。

## 我们如何使ML模型面对对抗性样本时更加强壮？ (How have we tried to make ML models more robust to adversarial examples?)

我们先介绍下两种防御方法：对抗性训练和防御性蒸馏。防御者如何试图使机器学习模型更加强壮并减轻对抗性样本的攻击效果。

对抗性训练旨在训练时主动产生对抗性样本，在测试时提取对抗性样本来改进模型的泛化。这个想法是由Szegedy等人首次提出的[SZS13]，但由于产生对抗性样本的成本太高而不实用。 Goodfellow等人展示了如何利用快速梯度符号方法低成本地产生对抗性样本，并且使得在训练过程中高计算效率地产生大批对抗性样本 [GSS14]。然后该模型被训练成将相同的标签分配给相对于原始样本的对抗性样本。例如：我们拍摄一张猫的照片，并对其进行扰动以欺骗模型，使其认为它是秃鹫，然后告诉模型这张照片仍然是一只猫。对抗训练的一个开源实现可以在[cleverhans](https://github.com/openai/cleverhans) 库中找到，其使用方法在下面的 [教程](https://github.com/openai/cleverhans/blob/master/tutorials/mnist_tutorial_tf.md) 中有说明。

防御性蒸馏平滑模型的决策表面在对抗方向被攻击者利用。蒸馏是一种训练过程，其中一个模型被训练以预测由先前训练的另一个模型输出的概率。蒸馏最初由Hinton等人提出。在[HVD15]中，其目标是用一个小模型来模拟一个大型的、计算成本很高的模型。防御性蒸馏有一个不同的目标，即简单地使最终模型的反应更加平滑，所以即使两个模型的大小相同也能起作用。训练一个模型来预测另一个具有相同架构的模型输出看起来是违反直觉的。它的工作原理是，第一个模型是用“硬”标签（图像100％概率是狗而不是猫）训练，然后第二个模型用“软”标签（图像95％概率是狗而不是猫）训练。第二个蒸馏模型对于诸如快速梯度符号法[PM16]或基于雅可比行列式显著图法[PMW16]的攻击更为鲁棒。这两种攻击的实现也分别在[这里]( https://github.com/openai/cleverhans/blob/master/tutorials/mnist_tutorial_tf.md) 和[这里]( https://github.com/openai/cleverhans/blob/master/tutorials/mnist_tutorial_jsma.md )的[cleverhans]( https://github.com/openai/cleverhans) 上提供。(已经404 。。2333)

## 一个失败的防御：“梯度掩蔽” (A failed defense: “gradient masking”)

大多数对抗性样本构建技术使用模型的梯度来进行攻击。换句话说，一张飞机的照片，他们测试在图像空间中往哪个方向移动使得图片识别为“猫”的概率增加，然后他们往那个方向进行移动（换句话说，扰乱了输入）。这样新修改后的图像被误认为是猫。

但是，如果没有梯度，如果对图像进行微小的修改不会导致模型的输出发生变化呢？这似乎提供了一些防御，因为攻击者不知道怎样去“推”图像。

我们可以很容易想到一些非常微不足道的方法来摆脱梯度。例如，大多数图像分类模型可以以两种模式运行：一种模式是输出最可能类别的标识，另一种模式是输出概率。如果模型的输出是“99.9％的飞机，0.1％的猫”，那么输入的一个微小的变化会给输出带来一个微小的变化，梯度告诉我们哪些变化会增加“猫”类的概率。如果我们在输出模式只是“飞机”的模式下运行模型，那么对输入的一个微小的变化根本不会改变输出，而梯度不会告诉我们任何事情。让我们做一个思考实验，如何通过以“最有可能的类”模式而不是“概率模式”运行它来防御对抗性样本。攻击者再也不能找到分类为猫的扰乱输入，所以我们可能会有一些防御。不幸的是，之前被归类为猫的图片现在仍被归类为猫。如果攻击者可以猜测哪些点是对抗性样本，这些点将仍然会被错误的分类。我们并没有使模型更加鲁棒，我们只是给了攻击者更少的线索来找出模型防御的漏洞。更不幸的是，事实证明攻击者有一个非常好的策略来猜测防御漏洞的位置。攻击者可以训练他们自己的模型，一个具有梯度的光滑模型，为他们的模型制作对抗性样本，然后将这些对抗性样本用于我们的非光滑模型。很多时候，我们的模型也会错误地分类这些样本。最后，我们的思想实验表明，隐藏梯度并没有达到我们的目的。

因此，我们称之为有缺陷的防御策略梯度掩蔽，这个术语在[PMG16]中有介绍。执行梯度掩蔽的防御策略通常导致在特定方向和训练点的邻域中模型变得非常平滑，这使得攻击者难以找到攻击方向的梯度去扰乱输入。然而，攻击者可以训练一个替代模型：一个模仿防御模型的副本，通过观察防御模型分配给攻击者输入的标签。[PMG16]中介绍了执行这种模型提取攻击的过程。然后攻击者可以使用替代模型的梯度来找到被防御模型错误分类的对抗性样本。在下面的图中，我们再现[PMS16]中对梯度掩蔽的讨论，我们用一维ML问题来说明这种攻击策略。对于高维问题，梯度掩蔽现象会加剧，但难以描述。

![]( http://cleverhans.io/assets/gradient-masking.png) 

令人惊讶的是，我们发现对抗性训练和防御性蒸馏都意外地表现出一种梯度掩蔽。如果我们将对抗性样本从一个模型转移到另一个模型，并且用这些防御之一进行训练，即使对第二个模型的直接攻击会失败，攻击通常也会成功[PMG16]。这表明两种训练方法都能使模型变平滑和消除梯度，而不是确保对多个点进行正确地分类。

## 打“地鼠”游戏 (Playing a game of “whack-a-mole”)

在“隐藏梯度”的游戏中，我们看到梯度掩蔽并不是很好的防御。它防御使用梯度的攻击者，但是如果攻击者知道我们正在使用这种防御，那么他们只需要切换到移植攻击。在安全术语中，这意味着梯度掩蔽不是一种自适应防御。

迄今为止提出的大多数针对对抗性样本的防御措施根本不起作用，但是有效的那些并不是自适应的。这意味着就像他们在玩一个打地鼠游戏一样：他们关闭了一些漏洞，但是让其他人打开。

对抗训练需要选择算法来产生对抗性样本。通常情况下，这个模型被训练成可以抵抗在一个步骤中产生的低成本对抗性样本，例如快速梯度符号方法一样。经过训练能抵制这些低成本对抗性样本，这个模型通常能成功地抵制同类低成本的新对抗性样本。如果我们使用高成本的、迭代的对抗性样本，就像[SZS13]中的那些例子，那么模型通常就会被愚弄。

保证适应性是具有挑战性的。灵感可以从差异隐私的框架中得到，它提供了随机算法不会暴露个人用户隐私的正式保证。这一保证不会对攻击者的知识或能力做出假设，因此能够面对未来由攻击者设计的假想攻击。

## 为什么很难防御对抗性样本？ (Why is it hard to defend against adversarial examples?)

对抗性样本很难防御，因为很难构建对抗性样本制定过程的理论模型。对抗性样本是许多ML模型（包括神经网络）的非线性和非凸的优化问题的解决方案。由于我们没有很好的理论工具去描述这些复杂的优化问题的解决方案，所以很难做出任何一种防御理论来排除一系列对抗性样本。

从另一个角度来看，对抗性样本很难防御，因为它们需要机器学习模型来为每一个可能的输入生成好的输出。大多数情况下，机器学习模型工作得很好，但只能处理遇到所有可能输入中的很少一部分。

由于可能的输入的量非常巨大，设计出真正自适应的防御是非常困难的。

## 其他攻击和防御方法 (Other attack and defense scenarios)

其他几种对机器学习的攻击也是难以防御。在本文中，我们专注于试图混淆机器学习模型测试过程的输入。但是其他类型的攻击是可能的，例如基于暗中修改训练数据的攻击，使得模型学习攻击者希望它进行的行为。

对抗性机器学习的一个亮点是差分隐私，我们实际上有理论上的观点，即某些训练算法可以防止攻击者从训练好的模型中恢复关于训练集的敏感信息。

将机器学习与攻击和防御都可能的其他场景进行比较是有趣的。

在密码学中，防御者似乎有优势。给定一系列合理的假设，例如加密算法的正确实现，防御者可以可靠地发送攻击者无法解密的消息。

在物理冲突中，攻击者似乎有优势。建造核弹比建造一个能够承受核爆的城市要容易得多。热力学的第二定律似乎意味着，如果防御要求将熵维持在某个阈值以下，那么即使没有明确的攻击者有意引起这种熵的增加，防御者也必然随着时间熵增加而最终失去。

监督学习的“没有免费午餐定理”[W96]指出，在所有可能的数据集进行平均，没有任何机器学习算法在测试时间的新点上比其他算法更好。乍一看，这似乎表明，所有的算法都同样容易受到对抗性样本。然而，“没有免费午餐定理”只适用于我们对问题结构不作假设的情况。当我们研究对抗性样本时，我们假设输入的小扰动不应该改变输出类别，所以一般形式的“没有免费午餐定理”并不适用。

正式揭露攻击者的鲁棒性和对清洁数据的模型表现之间的矛盾关系仍然是一个活跃的研究问题。在[PMS16]中，针对机器学习的对抗性样本的第一个“没有免费午餐定理”表明，在从有限的数据中学习时存在这样的矛盾。结果表明，防御者可以通过转向更丰富的假设类别来阻挠对抗性样本。然而，这种矛盾关系是由于没有合适的数据和学习算法来学习高保真模型所面临的挑战。

## 总结

对抗性样本的研究是令人兴奋的，因为许多最重要的问题在理论和应用方面都是开放的。在理论上，还没有人知道防御对抗性样本是否是一个理论上没有希望的努力（如试图找到一个通用的机器学习算法），或者是否存在一个最优策略会使防御者更有利。（如在密码学和差分隐私）。在应用方面，还没有人设计出真正强大的防御算法，可以抵抗各种对抗性样本的攻击算法。我们希望我们的读者能够得到启发，解决其中的一些问题。

## References

[BNL12] Biggio, B., Nelson, B., & Laskov, P. (2012). Poisoning attacks against support vector machines. arXiv preprint arXiv:1206.6389.

[GSS14] Goodfellow, I. J., Shlens, J., & Szegedy, C. (2014). Explaining and harnessing adversarial examples. arXiv preprint arXiv:1412.6572.

[HVD15] Hinton, Geoffrey, Oriol Vinyals, and Jeff Dean. “Distilling the knowledge in a neural network.” arXiv preprint arXiv:1503.02531 (2015).

[PM16] Papernot, N., & McDaniel, P. (2016). On the effectiveness of defensive distillation. arXiv preprint arXiv:1607.05113.

[PMG16] Papernot, N., McDaniel, P., Goodfellow, I., Jha, S., Berkay Celik, Z., & Swami, A. (2016). Practical Black-Box Attacks against Deep Learning Systems using Adversarial Examples. arXiv preprint arXiv:1602.02697.

[PMS16] Papernot, N., McDaniel, P., Sinha, A., & Wellman, M. (2016). Towards the Science of Security and Privacy in Machine Learning. arXiv preprint arXiv:1611.03814.

[PMW16] Papernot, N., McDaniel, P., Wu, X., Jha, S., & Swami, A. (2016, May). Distillation as a defense to adversarial perturbations against deep neural networks. In the 2016 IEEE Symposium on Security and Privacy (pp. 582-597).

[SZS13] Szegedy, C., Zaremba, W., Sutskever, I., Bruna, J., Erhan, D., Goodfellow, I., & Fergus, R. (2013). Intriguing properties of neural networks. arXiv preprint arXiv:1312.6199.

[W96] Wolpert, David H. (1996). The lack of a priori distinction between learning algorithms. Neural Computation
