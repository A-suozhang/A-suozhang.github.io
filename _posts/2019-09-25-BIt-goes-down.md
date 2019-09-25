---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读笔记《And The Bits Goes Down》           # 标题 
subtitle:   第一次单独把一篇论文拎出来        #副标题
date:       2019-09-25              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-zynq.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - dl
---

> 这篇文章暂且作为将来文献阅读的一个小Tempelate把，把每个部分主要写什么东西列举一下

# [And The BIT Goes Done:Revisting the Quantization of Neural Networks](https://arxiv.org/abs/1907.05686)
* Facebook Research Lab
* 1907
* [Code](https://github.com/facebookresearch/kill-the-bits)

## Abstract

> 这一部分一般是记录一些关键词，然而单独读Abstract的时候貌似对文章内部的东西还没太理解

* 针对ResNet设计的压缩方案
    * MobileNet的针对简单Conv，不适用于Res
* 减少访问内存次数(Memory Print)
* 与一般Vector Quantize的区别是对于Activation的重建做了处理

* **自己理解的核心**
    * 量化不是用与原先权重相接近，而是要保证最后Activation的差距不是特别大
    * 量化本质上还是k-means聚出来的，用code book去存
    * 用KD来做finetune

## Intro

1. 针对ResNet-like的结构，利用卷积的高相关性
    * structed Q
    * Product Q
    * Byte-aligned indexes
2. 一般的Quantize： 量化的误差随着传播会积累
    * 利用未压缩的网络guide压缩过程
3. 专注于Activation的Reconstruction


## Related Work

> 相关的研究，可以做一些拓展

* Low BIt Train
    * 2元或者3元量化，可以用bit操作代替conv来加速
    * 但是在Backprop的时候由于weight离散化而导致精度损失
* Vector/Product Quantize
    * **Idea**: decompose The original high-dimensional space into cartesian product（笛卡尔积-两个集合内部元素任意组合） of subspace，而subspace分别被量化
    * *作者认为单纯使用之前的优化方式(去拟合原来的w分布)是在优化错误的object function，并会使结果drift*
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925155414.png)
    * 作者貌似上面这张图表明这个观点(虽然我觉得不太直观就是了...)
* Prune 
* Different Architecture - MobielNet；ShuffleNet    

## Implemention

> 具体的实现方式，还是比较必要的

* Quantize
    * 目标是获得最后结果的最小Reconstruction Error，而不是单纯的Weight偏差
    * **传统的PQ(Product Quantization)** 将输入参数矩阵W按列拆分为m个SubVector，针对他们以L2范数为损失
        * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925111938.png)
        * 如果m=1就是Vector Q
        * 如果m=Cin，那么就等价于Scalar k-means
    * **本文的量化方式**
        * 作者还用一张图描述了：去保证量化之后的参数与之前差距不大，不能保证这一层的输出是靠谱的
        * 转而，Learning a codebook ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925112226.png)
            * 实际可用通过加权的k-means来实现（EM模式）
        * 本质上还是Han Song DC中的k-means+CodeBook的形式
            * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925155604.png)
    * 先逐层，然后再全局Finetune(防止Res带来的Shift)
        * 在Finetune这一环节用到了*Distillation*，用Uncompressed的网络作为TCH
    
## Expermiments

> 实验结果，详细还是简略还是看看心情

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925154249.png)
* 还把MaskRCNN的BackBone给Compress了来保证这个method是可行的

## Fracs

> 一些自己的思考和细碎的知识点

* 作者在实验环节提到了一点很有趣的： leverage a large collection of unlabelled images to improve the performance of a given architecture 
    * Suppoerted by [Billion-scale semi-supervised learning for image classification](https://www.semanticscholar.org/paper/Billion-scale-semi-supervised-learning-for-image-Yalniz-J%C3%A9gou/88ee291cf1f57fd0f4914a80b986a08a90d887f1)
    * 这种方式被叫做**Levearge The Unlabelled Images**


