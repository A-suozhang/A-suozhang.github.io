---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读《Mean teachers are better role models》          # 标题 
subtitle:   又一个自己的idea...   #副标题
date:       2019-10-28            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-nmb4.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
---

# [Mean teachers are better role models: Weight-averaged consistency targets improve semi-supervised deep learning results](https://arxiv.org/abs/1703.01780))

* Curious AI的一篇Work
* 现在已经是一个基操了
* 在这之前的SOTA主要是Temporal Ensemling
  * 对每个training sample的label Prediction取一个Exponential Average
  * 对差距特别大的做Penalty
  * *但是由于Targets每个epoch只改变一次*
* Mean-Teacher  
  * 平均化模型的Weights
  * 对TCH和STU的prediction加上一个consistency loss
    * 指引他们去perform consistently
  * Student - Learn As Before
  * Teacher - Generate Targets
  * 可达到Training with fewer label(和Semi想通)
* 一个好的分类器应该对相近的点给出一致的结果
  * 一种方式是对输入数据加入noise,尝试让网络学到更abstract的特征
  * Dropout
  * 基于Regularization的方法: 不是直接对数据点(0-d)求Classification Loss
    * 而对around Data Point的一个Manifold
    * 而做到将Decision Boundary推离Label Points