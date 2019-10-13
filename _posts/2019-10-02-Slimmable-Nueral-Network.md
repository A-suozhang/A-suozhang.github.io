---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读笔记《Slimmable Neural Network》          # 标题 
subtitle:   可变Width的NN       #副标题
date:       2019-10-2            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-jianxiong3.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 论文阅读
---

# Slimmable Neural Network
* Illinois Univ & ByteDance
* 2018.12
* Train a shared network with *switchable BN*
* Accuracy达到了Baseline
* could apply to tasks: Object Detction and Image Classification
* Runtime时候，能够on-the-fly的获得Accuracy与Latency的Trade-Off
* [开源代码](https://github.com/JiahuiYu/slimmable_networks)
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191002212209.png)
    * 各个模型的param是shared
    * 各个模型进行了Joint Training，对各个程度的模型都获得了更好的效果

## Related Works
* Dynamic Neural Network(Dynamic Comutation Graph)
* Conditional Normalization - *⌛这里可能会有新的需要了解的地方*
    * BN could encode Condition information -> Feature Representation 🤔？？

## Implemention
* **Switchable BN**
    * 一般来说，动态改变模型Channel数目(我们这里case里面当不同Slim程度的网络在训练中切换的时候会suffer)的网络训练会崩，可能原因是*由于Channel数目变了导致了mean和variance发生了变化，BN就不准了*为了解决这个问题，提出了*Switchable BN*
    * *Moving Average* to acculmulate mean and variance 
    * *Advantage*
        1. Extra Param少，可以忽略
        2. Runtime Overhead也可以忽略
    * 具体实现方式还需要参考代码
* **Incremental(Progressive) Training** 简单的正常训练，会导致超低的准确度(*就是因为上述的问题*)
    * 从小模型开始逐渐增进
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191002223534.png)
    * 实际的Training Process感觉区别不大

