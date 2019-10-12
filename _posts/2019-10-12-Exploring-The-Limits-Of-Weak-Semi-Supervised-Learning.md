---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读笔记《Exploring The Limits Of Weak Supervised Pretraining》          # 标题 
subtitle:   又是Facebook的文章...       #副标题
date:       2019-10-12            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-tz2.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 论文阅读
---

> 由于时间问题，这篇文章最终变成了草读...

# [Exploring The Limits Of Weak Supervised Pretraining](https://scholar.google.com/scholar_url?url=http://openaccess.thecvf.com/content_ECCV_2018/html/Dhruv_Mahajan_Exploring_the_Limits_ECCV_2018_paper.html&hl=zh-CN&sa=T&oi=gsb&ct=res&cd=0&d=8358288919170046195&ei=iBSfXdO5CMi2ywTcv7PYAg&scisig=AAGBfm368DTBQjMnx_giq93EFc4r29RbNQ)
* FAIR
* ECCV2018 
* 起因是ImageNet太小了，图片的标注有些困难了，开发了一种迁移学习的方法，从超大量的Social Media图片中，用大CNN（所谓的大CNN对Imganet来说也就就是ResNet101），做hashtag prediction
    * 把Social Media中的Hashtag，来作为弱Label(还真tm只有facebook能做，淦...)
    * 要革了人工标注的命

### 用了啥Dataset
* Instagram Dataset
    * 甚至是有一套采数据的流程...
    * 一个图片很可能对应多个Hashtag

### 怎么个Transfer法
* Full Network Finetune
    * 和正常的finetune无异
* Feature Transfer
    * 首先按照L2泛化训练一个网络
    * 后面接上Softmax直接去训后面这个Logistic Classifier
        * 据说后面这个能起到不错的效果

### 实验
* 超级多，能提点

> 真是读的最草率的一篇论文