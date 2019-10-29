---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读《Self-ensembling for visual domain adaptation》          # 标题 
subtitle:   又一个自己的idea...   #副标题
date:       2019-10-28            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-nmb4.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
---

# [Self-ensembling for visual domain adaptation](https://arxiv.org/pdf/1706.05208.pdf)

* 结合了Mean-Teacher和Temporal的ensemble方式
* VisDA2017 - ICLR2018
* Semi-Supervised方法的意义，减少GT Label的要求（减少人工成本）
* 从Synthetic Data到Real Data
* Confidence Thresholding & Class Balancing

### Other Way Of DA
* AutoEncoder
  * 将两个域的图片都Encode进了一个Latent Space
  * 做法是Split Model，对Representation分两部分；1. Shared Feture & Domain Sprcific
* Bifurcated（分支）
  * 训练一个classifier尝试去区分Target来自哪个Domain(有点类似于Incremental)
  * gradient reversal layer - encourage the feature extractor to 


