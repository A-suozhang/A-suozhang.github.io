---
layout:     post                    # 使用的布局（不需要改）
title:      OnlineLearning内容梳理           # 标题 
subtitle:            #副标题
date:       2020-01-28            # 时间
author:     tianchen                      # 作者
header-img:  img/1_28/nantong-1.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 常用
---

# Overview

* Life Long & Online Learning & Incremental  核心在于**可继续的学习**后者加上了在线更新以及内存受限等限制
  * 继续学习 - 持续调整模型


# Semi-Supervised Learning

* 利用少量标注数据和大量未标注数据进行训练
  * 随着在线的未标注数据补充进行训练


## 资源

* SemiSupervised paper list (Markdown)
* Survey-Online Learning On Edge (PPT)

---

* [SOTA List](https://paperswithcode.com/sota/semi-supervised-image-classification-on-cifar)
  * 包含了一系列paper with code
* [Realistic SSL](https://github.com/perrying/realistic-ssl-evaluation-pytorch)
  * Goodfellow的对比多种SemiSupervised方法
* [一些 SSL Implemention](https://github.com/semi-supervised-paper/semi-supervised-paper-implementation)
* [FAIR 开源的一系列SSL模型](https://github.com/facebookresearch/semi-supervised-ImageNet1K-models)


# Domain Adaptation

* 处理训练数据(源域 Source Domain)与测试数据(目标域 Target Domain)之间差异性大的问题
  * Unsupervised Domain Adaptation - 当目标域数据无label的情况

## 资源

* Unsupervised Domain Adaptation paper list (Markdown)
* Survey-Online Learning On Edge (PPT)
* [My Implemention for mean-teacher](https://github.com/A-suozhang/meanteacher-pytorch)

---

* [Transfer Learning资源汇总](https://github.com/jindongwang/transferlearning)
  * 相当全的一个git repo，整理了transfer learning相关的一系列内容
* [Salad](https://github.com/domainadaptation/salad)
  * 一个涉及semi-supervised以及domain adaptation的库,提供了一些数据集接口和一些方法，还比较完全
* [Awesome DA](https://github.com/zhaoxin94/awesome-domain-adaptation)
* [ImageCLEF Competition](https://www.imageclef.org/2014/adaptation)
* [VisDA Competition](http://ai.bu.edu/visda-2019/)
* [SOTA list](https://paperswithcode.com/task/unsupervised-domain-adaptation)





# Incremental(Continual) Learning

* 持续性学习，完成一系列任务

## 资源

* Survey Incremental Learning Markdown
* Survey Incremental Learning PPT

---

1. 他人总结的 Incremental Learning Reading List  [Awesome-Incremental-Learning](https://github.com/xialeiliu/Awesome-Incremental-Learning)
2. 比IL更广泛的Continual Learning Benchmark  [Continual-Learning-Benchmark](https://github.com/GT-RIPL/Continual-Learning-Benchmark)
    * 另外,这个组同时还写了 3 Scenario 和另外一篇IL的通用方法的论文,算是一个大组
3. 数据集 [CoRe50](https://vlomonaco.github.io/core50/benchmarks.html)
    * 很多论文中对数据集的处理方案还是用的Goodfellow文中创造SubTask的方案
4. 前NN时代的IL算法的源码和数据集整理 [link](https://github.com/vlosing/incrementalLearning)
5. 最近比较流行的集中IL算法的实现与比较 [link](https://github.com/GMvandeVen/continual-learning)
    * 包括了 EWC,LwF,ICaRL等主流方法
    * 提到了3 IL Sceneerio Paper,对现有的方法做了一个对比
6. 另外一个系统性的IL算法的复现代码 [code](https://github.com/arthurdouillard/incremental_learning.pytorch)


# Meta-Learning

* 学习如何去学习
* Few-shot Learning（从极少训练数据获得好的效果）是主要应用场景
* (没有展开...)


## 资源

* [Meta-Learning Framework](https://github.com/learnables/learn2learn)

# Fixed-Point Training

## 资源

* [Awesome Fixed-Point Training](https://github.com/A-suozhang/awesome-quantization-and-fixed-point-training)
  * Reference 中有一些其他资料
* Fixed Point Training PPT、

---

* [awesome-model-compression-0](https://github.com/ChanChiChoi/awesome-model-compression)
  * 其中有一些其他的awesome list的索引
* [awesome-model-compression-1](https://github.com/memoiry/Awesome-model-compression-and-acceleration)


