---
title: 如何将 Matplotlib 图像展示在 web 页面上
tags:
  - Matplotlib
  - Django
  - python
categories: 经验值
thumbnail: https://img.xungejiang.com/static/images/18-8-4/001.jpg
updated: '2018-08-04 15:08:00'
date: 2018-08-04 15:08:00
---

我们知道 Matplotlib 是一个非常强大的 Python 画图工具，其不仅可以画条形图、饼状图等统计图，也可以画以像素构成的图像。MATLAB 能画的图像，Matplotlib 通过 Python 语言也能画。

做项目的时候遇到一个需求，就是如何将网页后端生成的 Matplotlib 图像展示在前端页面上。尝试使用了如下几种方法，在此记录一下。

<!-- more -->

## 使用 mpld3 包

这是一个相对简单并且改动较小的方法，只需要在后端改一下 import 就可以，具体使用方法参照[官方教程](http://mpld3.github.io/quickstart.html)。

但是实现的时候遇到了一些问题

- 不适合大数据可视化的处理，当图像超过几千个元素时，前端展示的图像会有一定的模糊；
- 使用时必须联网；
- 一些 Matplotlib 的方法在 mpld3 中缺失。


## 保存在网页服务器的 static 目录下

该方法易于实现，首先将网页后端的图像保存到后端服务器的 static 目录下，前端再从 static 目录下读取图片进行展示。

但是依然存在一些问题

- 无法判断响应时间。因为后端生成图片的时间未知，所以只能采用在前端延时展示，这样也浪费时间资源；
- 前后端分离的项目中，前端访问后端 static 目录路径时不方便。

## 使用请求的方式将图像传到前端

该方法将图像以请求的方式传到前端，前端只需将 `<img>` 标签的 src 属性赋值为后端的请求路径即可。

该方法可以在后端生成完图像后再发送给前端，无需设置延时获取图像，但也遇到一些问题

- 项目中后端获取前端的请求后需要返回两个请求，一个是表格数据，一个是图像，这样代码就比较冗余；
- 由于 Matplotlib 生成的图像有白边，而只有加上 `fig.savefig('a.png', bbox_inches='tight', pad_inches=0.0)` 这句代码时才能去除白边，而发送请求只能发送 fig ，所以前端显示的图像有白边。

## 将图像以 Base64 格式发送给前端

该方法也是本人最终采取的方法。原理是在调用 savefig 方法时不存储为图像，而是存储为二进制格式，二进制格式再转化为 Base64 格式，并将其发送给前端，前端只需要将 `<img>` 标签的 src 属性赋值为后端发送的 Base64 字符串即可。

后端代码如下所示

```
from io import BytesIO
import base64
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(1, 1))
...
sio = BytesIO()
fig.savefig(sio, format='png', bbox_inches='tight', pad_inches=0.0)
data = base64.encodebytes(sio.getvalue()).decode()
src = 'data:image/png;base64,' + str(data)
```

将最后一行的 `src` 传到前端即可展示。

该方法优点如下

- 该方法可以在后端生成完图像后再发送给前端，无需设置延时获取图像；
- 该方法只需要往前端发送一次请求，代码更加精简；
- 调用了 savefig 方法，可以去除白边。

所以几乎解决了之前方法的所有痛点