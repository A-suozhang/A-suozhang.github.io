---
layout:     post                    # 使用的布局（不需要改）
title:      DL的一些统计指标与标准           # 标题 
subtitle:   尝试记一些这样的短指令...        #副标题
date:       2019-09-24              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-term.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - dl
---

# Precision & Recall -> mAP
* Precision 识别为正确的样本中有多少是真正正确的 - 可以很保守
    * TP/(TP+FP)
* Recall 客观正确的样本有多少能被识别出来 - 可以很激进
    * TP/(TP+FN)

* 比如医学模型我们需要Recall高一点
    * *宁可错杀，不可放过*
* 比如买存在故障的东西我们希望Precision高一点
    * *保守一点好*

## 混淆矩阵
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190924125659.png)

## PR 曲线  
* 纵轴是Precision横轴是Recall，定义更直观，但是实际应用起来不如ROC好用(后者能够在样本不均衡的条件下获得更好的效果)

## AP
* AP就是PR曲线的AUC
* 由于AP是针对每一个类的，mAP就是所有类的平均

## ROC曲线 - 被试者曲线
* 纵轴 Recall - (TP)/(TP+FN)  横轴 FP/(FP+TN)
* 一般是一个阶梯型

## AUC值
* 描述的是ROC曲线的 Area Under Curve(曲线下面积)
