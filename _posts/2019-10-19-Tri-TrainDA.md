---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读《Asymmetric Tri-training for Unsupervised Domain Adaptation》          # 标题 
subtitle:   和Semi-Supervised有点异曲同工？   #副标题
date:       2019-10-18            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-ucentre1.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
     - 迁移学习
---

# [Asymmetric Tri-training for Unsupervised Domain Adaptation](https://arxiv.org/pdf/1702.08400.pdf)

* Univ. Of Tokyo
* 解决的还是在Target Domain中标注数据过于Expensive
* 之前的一些文章方向主要在于*Match The Distribution*
    * 还有一些文章尝试去学出*共通的Feature Representation*
    * 本文从*Artificially labelling Distribution入手*
* 一些Transdutive的Method利用Target Sample与Source Sample的KNN
* 有文章证明*Training A Network with Pseudo Data等价于Entropy regularization*
* **Self-Training**的场景描述的是
    * 当本身的置信度基本正确
    * 通过Self-training可以Further Improve性能  
    * *Co-Training*则是利用两个模型造成Pseudo-Data
    * The generalization ability of co-training is theoretically ensured
        * ([Balcan et al., 2004](https://papers.nips.cc/paper/2578-co-training-and-expansion-towards-bridging-theory-and-practice) [Dasgupta et al., 2001](https://papers.nips.cc/paper/2040-pac-generalization-bounds-for-co-training))
    * 比Co-Training再更进一步*Tri-Training*
        * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191019125950.png)
* **Asymmetric Tri-Training**
    * Unsupervised场景下使用2个Classifier去构建Pseudo Label
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191019123908.png)
* 号称比当时的SOTA能高10个点
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191018224415.png)
    * 也是需要这样一个保险机制的
* 文章中提到BN能够Whiten最后的OUTPUT Hidden layer
    * 通过在最后一个BN层，让各个域的Distribution更接近
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191019131113.png)