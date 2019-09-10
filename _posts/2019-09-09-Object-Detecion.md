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
        * 分别计算PR(这里计算的是框子的数目,不是类别)并画出PR曲线
        * PR曲线下的积分面积就是AP
* mAp - meanAP - AP是基于某一类的，mAp就是求AP关于类的平均

## Methods
### Faster-RCNN 2015
* ~~祖宗~~
* CNN end2end训练，速度几乎实时
    * 一次性完成区域提取，特征提取bounding box和回归
* 用RPN(Region Proposal Network)以“Anchor”的概念，代替了Selective Search
    * 以Feature Map（经过降采样之后不大的）上的每一个点作为中心，在**原图**映射为9个anchor  
    * 以IOU作为前景后景的打分标准
* 经过RPN，我们将原图中产生的anchor box返回到Feature Map，得到了一些Proposal，然后做一个ROI Pooling（将抠出来的Proposal转化为统一大小）
* Loss是分类损失以及位置回归误差之和
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909152753.png)
###  Yolo（You Only Look Once） 2015
* **简单暴力**所有东西拿CNN硬干
* 第一个1-Stage的网络，抛弃了区域提取+验证的2-stage思想，直接将原图分为S*S的grid cell,每个里面塞B个Bounding Box,推测C类，每个BBox预测5个value（xywh+conf）最后需要预测的Tensor为 ```S*S*(B*5+C)```
* *How 2 Get Box* 每一个BBox都会对应一个confidence，等价于GT与该Bbox的IOU（如果这个Grid Cell里面没有Obj则conf=0，判断方式是gtbox的中心是否落在这个gridcell里面）然后Bbox的conf再和20个类别所得的置信度相乘，获得每一类的最终置信度，并经过一个NMS（防止一个Obj对应着太多的Box）
* Loss = 定位误差 + IOU误差 + 分类误差
* 速度极快，精度也随着改进跟上了
    * 引入Res，引入Anchor，加入BN
* 发展历史：
    * Yolo v1：
        * Features： 1. 用Grid Cell划分区域之后，对每个区域独立进行检测（分治法） 2. 完全End2End 3.应用了Leaky relu
        * Shortcomings： 1. 位置准确度不足 2，对小物体效果差（由于每个格子最多检测出一个物体） 3. Recall比2-Stage的方法小
    * Yolo V2 （Yolo9000） - 主要改善Recall以及定位精度
        * 加入了BatchNorm
            * 🤔Why Could BN Help: 网络层数加深，整体数据的分布会越来越偏，由BN强制拉到一个比较标准的分布（不然会发生Internal Covariance Shift）
                * 本质上还是把数据拉到损失函数比较敏感（梯度比较大的地方）便于收敛
        * Anchor Box的引入
            * RPN的核心也是Anchor，通过anchor，让feature map上的点映射回到原图的点，这样需要预测的就不是直接的坐标而是偏移量了
            * Yolo V2把全连接换成了Anchor Box方式进行预测
                * 
        * 用K-means训练Bbox的Size
        * 添加了细粒度模块添加了一个PassThrough层，将相邻的特征按通道叠加而不是按空间叠加，这样让高低分辨率的特征能够串接起来
            * [这篇文章]()表示这种方法和Res异曲同工，我没理解
            * 🤔:Why Res Help(我感觉这个观点有点意思) Resnet通过一个shortcut feedback，学习的是f(x)-x，*举一个比较极端的例子*，假设f(x)=x（恒等映射）学习一个g(x) = f(x)-x=0比学习一个x要简单（而且NN的初始化一般都是0均值的）
        * MultiScale-Training 多尺度训练
    * Yolo V3 （Even Faster）

            
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

## 🤔🤔🤔Thoughts
### 




## SubFileds (~~术业有专攻~~)
### 多尺度目标检测 MultiResolution
* Timeline
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909183225.png)
* 

