---
layout:     post                    # 使用的布局（不需要改）
title:      Object Detection简述              # 标题 
subtitle:   看看这一年目标检测领域又背着我发展了多少(雾)        #副标题
date:       2019-09-09              # 时间
author:     tianchen                      # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - DL
    - 综述
---
# Object Detection
* 从去年这个时候做SSD-FPGA之后，就没怎么关注过这一块了，在DL中一年不关注一个领域的发展可见一斑（特别还是Detection这么火的一个领域），来补课了
* 文章来自于[Arxiv](https://arxiv.org/abs/1905.05055)又看的[zhihu](https://zhuanlan.zhihu.com/p/72838705)的翻译版，我自己再夹杂一些私货，算是二次咀嚼了，~~反刍（不是)~~

## Metrics
* Precison = True Positive/All Positve
    * 最后判断为Positive的有多少是正确的
* Recall = True Positive/(TP+FN)->All Positive(GT) 
    * GT中有Positive多少被判断出来了
* AP - Average Precision
    * 不同Recall下的Precision的Mean
    * 详细计算方法参考[这个教程](https://zhuanlan.zhihu.com/p/72838705)
        * 分别计算PR并画出PR曲线
        * PR曲线下的积分面积就是AP
* mAp - meanAP - AP是基于某一类的，mAp就是求AP关于类的平均



## Methods
### Faster-RCNN 2015
* ~~祖宗~~
* CNN end2end训练，速度几乎实时
    * 一次性完成区域提取，特征提取bounding box和回归
* 用RPN(Region Proposal Network)以“Anchor”的概念，代替了Selective Search
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909152753.png)
###  Yolo（You Only Look Once） 2015
* 第一个1-Stage的网络，抛弃了区域提取+验证的2-stage思想，直接将原图分为S*S的grid cell,每个里面塞B个Bounding Box,推测C类，每个BBox预测5个value（xywh+conf）最后用NMS去掉多余的Bbox
* 速度极快，精度也随着改进跟上了
    * 引入Res，引入Anchor，加入BN
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909152711.png)
### SSD (Single Shot Detector)
* 引入了多目标检测
    1. 多尺度特征图(大的特征图检测小物体（分辨率更高）) - 借鉴了金字塔结构
    2. 摈弃FC，用卷积作为来检测
    3. 保留了先验框（Faster RCNN中的**Anchor**）为每一个单元格子都设置长宽比不同的先验框，是BBox的基准，对于每个anchor box都独立作出判别
    4. 训练中Ground Truth与Box的配准： yolo较为naive，gt的中心在那个单元格，就找（该grid cell中）与其IOU最大的bbox负责
        * 而SSD则更为“谨慎”，对于每一个gt都找出图中所有的anchor box（比前面少了当前的grid cell中）与其IOU最大的，然而这样会造成负样本过多（超多的anchorbox没有对应的gt），因而需要降低标准，对每一个anchor，如果与gt的iou大于某一阈值，也看做匹配
### FPN（Feature Pyramid） 2017
* 金字塔的结构，更好适应多尺度
### RetinaNet
* 注重于*one-stage为何不如2-stage精确度高的问题*
    * 前景后景之间的不平衡 （背景框太多让训练跑偏）
    * 样本难以区分程度差距大 （需要花更多精力在难区分样本上）
* 通过**Focal Loss**解决
### TridenNet （~~在深海使用三叉戟~~）2019
* 关注*尺度变化的问题，研究不同感受野的优劣*
* 将传统conv换*空洞卷积*，从而改变不同分支的感受野的大小

