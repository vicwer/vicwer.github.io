---
layout: post
title: caffe中卷积的计算方式
tags: [caffe, convolution]
---

### im2col

将要卷积的窗口展成列，以达到并行计算的目的，空间换时间，feature map展开如下图所示

> 其中输入feature map的宽高分别为W，H，输出feature map的宽高分别为W1，H1，卷积核大小为KxK，C是通道数

![im2col](/images/posts/2017-05-01/im2col1.PNG)

卷积核以feature map的方式同样展开，然后进行卷积，如下图所示

![im2col](/images/posts/2017-05-01/im2col2.PNG)

此处举两个实例，如下所示

> 其中inc=3, inh=3, inw=3, outc = 2, outh=2, outw=2, stride=1

![im2col](/images/posts/2017-05-01/im2col3.PNG)

> 其中stride=2

![im2col](/images/posts/2017-05-01/im2col4.PNG)

caffe在CPU模式下运行时会调用openblas加速，并用openmp优化