---
title: Pytorch 图像分类实战 —— ImageNet 数据集
tags:
  - pytorch
  - imagenet
categories:
  - 经验值
date: 2019-07-26 17:02:52
updated: 2019-07-26 17:02:52
---

Pytorch 深度学习框架和 ImageNet 数据集深受科研工作者的喜爱。本文使用 Pytorch 1.0.1 版本对 ImageNet 数据集进行图像分类实战，包括训练、测试、验证等。

<!-- more -->

## ImageNet 数据集下载及预处理

数据集选择常用的 `ISLVRC2012` (ImageNet Large Scale Visual Recognition Challenge)

**下载地址：**

- 测试集 [http://www.image-net.org/challenges/LSVRC/2012/nnoupb/ILSVRC2012_img_test.tar](http://www.image-net.org/challenges/LSVRC/2012/nnoupb/ILSVRC2012_img_test.tar)(12.7GB)
- 验证集[http://www.image-net.org/challenges/LSVRC/2012/nnoupb/ILSVRC2012_img_val.tar](http://www.image-net.org/challenges/LSVRC/2012/nnoupb/ILSVRC2012_img_val.tar)(6.3GB)
- 训练集[http://www.image-net.org/challenges/LSVRC/2012/nnoupb/ILSVRC2012_img_train.tar](http://www.image-net.org/challenges/LSVRC/2012/nnoupb/ILSVRC2012_img_train.tar)(138GB)

**预处理：**

为了使用 Pytorch 自带的 DataLoader 函数进行数据集加载，我们需要将每一个相同类的图片放到相同的文件夹。

训练集只需要解压缩即可：

```shell
mkdir train && mv ILSVRC2012_img_train.tar train/ && cd train
tar -xvf ILSVRC2012_img_train.tar && rm -f ILSVRC2012_img_train.tar
find . -name "*.tar" | while read NAME ; do mkdir -p "${NAME%.tar}"; tar -xvf "${NAME}" -C "${NAME%.tar}"; rm -f "${NAME}"; done
cd ..
```

但是验证集图片都在一个文件夹，需要重新分类：

```shell
mkdir val && mv ILSVRC2012_img_val.tar val/ && cd val && tar -xvf ILSVRC2012_img_val.tar
wget -qO- https://raw.githubusercontent.com/soumith/imagenetloader.torch/master/valprep.sh | bash
```

## 代码详解

### 参数设置

参数设置的方式有很多种，有的人喜欢直接在主文件中进行设置；有的人喜欢用 argparse 这个模块；也有人喜欢用 json 格式的文件，我个人喜欢单独创建个 Python 类，以类属性的形式定义参数，详情见下：

```python
class DefaultConfigs(object):
    # 1.string parameters
    train_dir = "/home/ubuntu/share/dataset/imagenet/train"
    val_dir = '/home/ubuntu/share/dataset/imagenet/val'
    model_name = "resnet18"
    weights = "./checkpoints/"
    best_models = weights + "best_model/"

    # 2.numeric parameters
    epochs = 40
    start_epoch = 0
    batch_size = 16
    momentum = 0.9
    lr = 1e-3
    weight_decay = 1e-4
    interval = 10
    workers = 12

    # 3.boolean parameters
    evaluate = False
    pretrained = False
    resume = False
```

### 评价指标

当我们需要评价一个模型的准确率时，需要输出 top1、top5 等准确率，使用下面函数进行封装。其中 `AverageMeter` 类可快速计算多个值的平均值等。

```python
class AverageMeter(object):
    """Computes and stores the average and current value"""

    def __init__(self, name, fmt=':f'):
        self.name = name
        self.fmt = fmt
        self.reset()

    def reset(self):
        self.val = 0
        self.avg = 0
        self.sum = 0
        self.count = 0

    def update(self, val, n=1):
        self.val = val
        self.sum += val * n
        self.count += n
        self.avg = self.sum / self.count

    def __str__(self):
        fmtstr = '{name} {val' + self.fmt + '} ({avg' + self.fmt + '})'
        return fmtstr.format(**self.__dict__)


def accuracy(output, target, topk=(1,)):
    """Computes the accuracy over the k top predictions for the specified values of k"""
    with torch.no_grad():
        maxk = max(topk)
        batch_size = target.size(0)

        _, pred = output.topk(maxk, 1, True, True)
        pred = pred.t()
        correct = pred.eq(target.view(1, -1).expand_as(pred))

        res = []
        for k in topk:
            correct_k = correct[:k].view(-1).float().sum(0, keepdim=True)
            res.append(correct_k.mul_(100.0 / batch_size))
        return res
```

### 验证模型准确率

当验证模型和训练模型时都需要使用验证集验证模型准确率，来指导下一步操作。注意需要将 `model` 切换为 `evaluate` 模式。其中 `torch.no_grad()` 表示计算时不会改变模型梯度。

```python
def validate(val_loader, model, criterion):
    batch_time = AverageMeter('Time', ':6.3f')
    losses = AverageMeter('Loss', ':.4e')
    top1 = AverageMeter('Acc@1', ':6.2f')
    top5 = AverageMeter('Acc@5', ':6.2f')

    # switch to evaluate mode
    model.eval()

    with torch.no_grad():
        end = time.time()
        for batch_id, (images, target) in enumerate(val_loader):
            images, target = images.to(device), target.to(device)
            # compute output
            output = model(images)
            loss = criterion(output, target)

            # measure accuracy and record loss
            acc1, acc5 = accuracy(output, target, topk=(1, 5))
            losses.update(loss.item(), images.size(0))
            top1.update(acc1[0], images.size(0))
            top5.update(acc5[0], images.size(0))

            # measure elapsed time
            batch_time.update(time.time() - end)
            end = time.time()

            if (batch_id + 1) % config.interval == 0:
                print('Acc@1: {top1.avg:.3f}\tAcc@5: {top5.avg:.3f}\tTime: {batch_time.val:.2f}\tID: {batch_id:d}'
                      .format(top1=top1, top5=top5, batch_time=batch_time, batch_id=(batch_id + 1) * config.batch_size))

        print(' * Acc@1 {top1.avg:.3f} Acc@5 {top5.avg:.3f}'
              .format(top1=top1, top5=top5))
    return top1.avg
```

### 训练模型

注意需要将 `model` 切换为 `train` 模式。

```python
def train(train_loader, model, criterion, optimizer):
    batch_time = AverageMeter('Time', ':6.3f')
    data_time = AverageMeter('Data', ':6.3f')
    losses = AverageMeter('Loss', ':.4e')
    top1 = AverageMeter('Acc@1', ':6.2f')
    top5 = AverageMeter('Acc@5', ':6.2f')

    # switch to train mode
    model.train()

    end = time.time()
    for batch_id, (images, target) in enumerate(train_loader):
        # measure data loading time
        data_time.update(time.time() - end)
        images, target = images.to(device), target.to(device)

        # compute output
        output = model(images)
        loss = criterion(output, target)

        # measure accuracy and record loss
        acc1, acc5 = accuracy(output, target, topk=(1, 5))
        losses.update(loss.item(), images.size(0))
        top1.update(acc1[0], images.size(0))
        top5.update(acc5[0], images.size(0))

        # compute gradient and do SGD step
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()

        # measure elapsed time
        batch_time.update(time.time() - end)
        end = time.time()

        if (batch_id + 1) % config.interval == 0:
            print('Acc@1: {top1.avg:.3f}\tAcc@5: {top5.avg:.3f}\t'
                  'Loss: {losses.val}\tTime: {batch_time.val:.2f}\tID: {batch_id:d}'
                  .format(top1=top1, top5=top5, batch_time=batch_time,
                          losses=losses, batch_id=(batch_id + 1) * config.batch_size))

    print(' * Acc@1 {top1.avg:.3f} Acc@5 {top5.avg:.3f}'
          .format(top1=top1, top5=top5))
```

### 主体函数

注意在数据集加载时，`train_loader` 的 `shuffle` 为 `True`。

```python
def main():
    global best_acc

    if config.pretrained:
        print("=> using pre-trained model '{}'".format(config.model_name))
        model = models.__dict__[config.model_name](pretrained=True)
    else:
        print("=> creating model '{}'".format(config.model_name))
        model = models.__dict__[config.model_name]()
    model.to(device)

    normalize = transforms.Normalize(mean=[0.485, 0.456, 0.406],
                                     std=[0.229, 0.224, 0.225])

    criterion = nn.CrossEntropyLoss().to(device)
    optimizer = torch.optim.SGD(model.parameters(), config.lr,
                                momentum=config.momentum,
                                weight_decay=config.weight_decay)

    if config.resume:
        checkpoint = torch.load(config.best_models + "/model_best.pth.tar")
        config.start_epoch = checkpoint['epoch']
        best_acc = checkpoint['best_acc']
        model.load_state_dict(checkpoint['state_dict'])
        optimizer.load_state_dict(checkpoint['optimizer'])

    train_loader = torch.utils.data.DataLoader(
        datasets.ImageFolder(config.train_dir, transforms.Compose([
            transforms.RandomResizedCrop(224),
            transforms.RandomHorizontalFlip(),
            transforms.ToTensor(),
            normalize,
        ])),
        batch_size=config.batch_size, shuffle=True,
        num_workers=config.workers, pin_memory=True)

    val_loader = torch.utils.data.DataLoader(
        datasets.ImageFolder(config.val_dir, transforms.Compose([
            transforms.Resize(256),
            transforms.CenterCrop(224),
            transforms.ToTensor(),
            normalize,
        ])),
        batch_size=config.batch_size, shuffle=False,
        num_workers=config.workers, pin_memory=True)

    if config.evaluate:
        validate(val_loader, model, criterion)
        return

    for epoch in range(config.start_epoch, config.epochs):
        adjust_learning_rate(optimizer, epoch)

        print('\nEpoch: [%d | %d]' % (epoch + 1, config.epochs))

        train(train_loader, model, criterion, optimizer)
        test_acc = validate(val_loader, model, criterion)

        # save model
        is_best = test_acc > best_acc
        best_acc = max(test_acc, best_acc)
        save_checkpoint({
            'epoch': epoch + 1,
            "model_name": config.model_name,
            'state_dict': model.state_dict(),
            'acc': test_acc,
            'best_acc': best_acc,
            'optimizer': optimizer.state_dict(),
        }, is_best)
```

## 总结

本文使用的 Pytorch 版本为 1.0.1，且暂时只适用于 ImageNet 数据集，其他数据集需要一定地修改，完整代码地址如下：[https://gist.github.com/xunge/d7be591bc1b41350273a61722c0d398a](https://gist.github.com/xunge/d7be591bc1b41350273a61722c0d398a)

## 参考资料

- [从实例掌握 pytorch 进行图像分类](http://spytensor.com/index.php/archives/21/)
- [pytorch/examples](https://github.com/pytorch/examples/blob/99a83a26b33622945eaed0d20614943a16f45a43/imagenet/main.py)