---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读《When Semi-Supervised Learning Meets Transfer Learning》          # 标题 
subtitle:   好的又是一个被想到的Idea   #副标题
date:       2019-11-21            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-xueyuanroad1.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
---

#  🌟 [When Semi-Supervised Learning Meets Transfer Learning: Training Strategies, Models and Datasets](https://arxiv.org/abs/1812.05313)

* Combine The Semi scheme in Finetuning from the Pretrained Model
* **Incorporate SSL Into Finetune**
* Did Comprehensive Empirical analysis 3 Conclusion:
    * The SSL Gain from full-supervised baseline(with fewer label) is smalller
    * When Domain Shifts, SSL Better
    * Some SSL Methods can outperform full-label training

## Related Work

* SSL
  * Consistency-Regularization-based
    * Ladder Network
    * Pi-Model
    * Mean-Teacher
  * GANs & Adversarial Training
    * VAT - Virtual Adversrail Loss
  * Entropy-based SSL
    * Entropy-Minmization
  * Co-Training
    * Tri-Learning - Relabelling
* Transfer Learning
  * Finetune 

## Datasets

* Transfer from Imagenet
  * Indoor (67 Scene, 6700)
  * CUB200
  * MURA (医学图像-骨骼的CT?)

## Experiments

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191121141351.png)
  * 20 - Epochs
  * 1k labels
  * F+T Augmentation
    * F - HorizontalFlip
    * T - RandomTranslation
    * N - Gaussian Noise

##　Conclusion

* *when you have enough labeled images,better pre-trained models seem to counteract the influenceof SSL methods.*