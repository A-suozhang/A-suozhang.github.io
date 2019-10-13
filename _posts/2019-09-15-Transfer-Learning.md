---
layout:     post                    # 使用的布局（不需要改）
title:      迁移学习 Transfer Learning            # 标题 
subtitle:   能不能举一反三呢？        #副标题
date:       2019-10-12              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-wukang.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 综述
---


# Transfer Learning 迁移学习

> 这个Field还是比较大，而且和Multitask啊Meta-Learning之间都有很多的勾连，所以系统入门比较困难，更多的还是一个感性的认知
* 和数据挖掘等领域联系也很紧密
* 会很玄幻...AAAI2017还有Distant Transfer Learning拿人脸来识别飞机 ...s


## What 2 Solve 能做啥
* 解决大数据量的产生但是标注少的问题
* 普适性和专门化的矛盾

## Target 目标
* 本身并不是面向NN Training的，曾经是一个比较数学的ML的问题
* 将Well-Trained的模型的知识以某种方式分享给新的模型，从而避免**Training From Scratch**
* 一个核心问题是**找到新问题和原来问题之间的相关性**

## Taxonomy 分类
* 比较权威的杨强教授的[A Survey On Transfer Learning(2010)]()
* 几种常见的操作
    * 标注的数据不足的时候 -- 选类似的有标注的数据
    * 计算资源不足的时候/个性化任务 -- 把训练好的大模型迁移到我们的task中
* 几种常见的基底
    * 基于样本(Instance-based)
        * 直接对更接近的样本赋予更高的权限 
    * 基于特征(Feature-based)
        * 寻找一个新的特征空间，将两者的特征映射到同一空间处理(*目前比较有玩头的*)
    * 基于模型(Model-based)
        * 参数共享，比如finetune
    * 基于关系(Relation-based)
        * 挖掘类比-最玄学
* Inductive(归纳) TF （个人感觉这一类可能不是狭义上我们所认为的TF，下面的才是）
    * MultiTask --**Source/Target Domain Label Both Available**
        * 数据Label全都有，Task效果全都要
        * 最Naive的做法是前面的层共享，在最后直接加fc去fit不同的任务
        * Example
            * [Cross-stitch Networks for Multi-task Learning](https://arxiv.org/abs/1604.03539)
                * 用矩阵的Linear Combination融合特征(没细读，没理解和Naive的方法有多少区别  )
            * [Multi-task Self-Supervised Visual Learning](https://link.zhihu.com/?target=http%3A//openaccess.thecvf.com/content_ICCV_2017/papers/Doersch_Multi-Task_Self-Supervised_Visual_ICCV_2017_paper.pdf)
        * Question
            1. 网络架构的设计 - *去尽可能的利用转化信息（特征）*
            2. 分配训练权重： 由于数据集的体量可能有区别，类型可能也有区别，训练很有可能*被某个Task所Dominant*
        * [Survey](http://ruder.io/multi-task/)
    * Self-Taught -- **Source Unavailable; Target Available**
        * 比如用ImageNet提取到的特征用在其他Image Classification的
        * *Concerning Catastrophic Forgetting* 作者观点说*针对单一数据训练出来的模型，到另外一个Task基本都会迅速忘记,导致跨任务的泛化能力十分差*
            * 常见的解决是Freeze一部分，或者是加新的分支(和之前IL的调研吻合了)
* Transductive(转移) --**Source Domain Available;Target Unavailable**感性的来看比Inductive要难
    * Domain Adaptation
        * 目标是要学习出 Domain-Invariant的Feature
        * *将不同源领域的数据映射到同一个特征空间*
        * 不要求训练以及测试数据同分布
        * 要防止学习Overfittig于Source Domain （~~显而易见的问题~~）    
        * 现在的常规操作基本都不得不利用到Target Domain的一些信息，去尝试缩小covariance shift,还不能够完全利用Source Domain（那就通用人工智能了吧...）
    * [Unsupervised Domain Adaptation by Backpropagation](https://arxiv.org/abs/1409.7495)
    * [Learning Transferable Features with Deep Adaptation Networks](https://link.zhihu.com/?target=http%3A//proceedings.mlr.press/v37/long15.pdf)
    * Sample Selection Bias
    * Covariance Shift

