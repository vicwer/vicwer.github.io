---
layout: post
title: Faster R-CNN初涉
tags: [目标检测, 深度学习, rcnn]
---

### Faster R-CNN简介

- 目的

提高提取proposal的速度。

- 输入与输出

输入：一张图像
输出：目标检测框，所属类别以及概率

- 特点

RPN代替selective search
RPN与Fast R-CNN共享卷积特征
完全实现端到端的目标检测

### RCNN系列发展

- R-CNN

思路框架：Input Image(by selective search) + crop/wrap + CNN + SVM
缺点：SS得到的每个proposal都要传入CNN，计算量大

- SPP-Net

思路框架：Input Image + CNN + (by selective search)SPP + SVM
缺点：无法在微调阶段反传残差，微调只能更新SPP层后面的全连接层

- Fast R-CNN

思路框架：Input Image( and selective search) + CNN + RoI pooling + FC*2(reg loss && softmax loss)
缺点：selective search成为瓶颈，为何还用selective search提取候选框？

### Faster R-CNN原理

把region proposal提取和Fast R-CNN部分融合到一个网络模型(RPN)

#### 网络大致流程
![faster-rcnn_network](/images/posts/2017-04-25/faster-rcnn_network.PNG)

#### RPN流程

- 得到最终用来预测的feature map: Input Image -> conv+relu(如5层ZF) -> conv+relu -> feature map(51*39*256)
- 计算Anchors: 在每个feature map上的每个特征点预测多个region proposals. 即每个特征点映射回原图的感受野的中心点成一个基准点，然后围绕这个基准点选取k个不同scale, aspect ratio的anchor. 可以只找大致位置，位置精修后续工作会完成。论文选取了{128x128, 256x256, 521x521} x {1:1, 1:2, 2:1}. 然后根据与ground truth的覆盖率给它正(IOU>0.7)负(IOU<0.3)标签，让它学习里面是否有object即可
-  取目标候选框：对于每个anchor，首先在后面接上一个二分类softmax，有2个score输出用以表示其是一个物体的概率与不是物体的概率(pi),然后再接上一个bounding box的regression输出代表这个anchor的4个坐标位置(ti)。

#### RPN与Fast R-CNN共享卷积层特征
![rpn_faster_share_conv](/images/posts/2017-04-25/rpn_faster_share_conv.PNG)

#### 检测流程

- 检测流程conv1-conv5

![conv1-conv5](/images/posts/2017-04-25/conv1-conv5.PNG)

- 检测流程RPN

![rpn](/images/posts/2017-04-25/rpn.PNG)

- 检测流程ROI

![roi](/images/posts/2017-04-25/roi.PNG)

### 计算量

最后附一张RCNN网络的计算量统计

![macc](/images/posts/2017-04-25/macc.png)
