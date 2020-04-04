---
layout:     post                    # 使用的布局（不需要改）
title:      Training Techniques           # 标题 
subtitle:   炼丹工人的基本修养   #副标题
date:       2020-02-06            # 时间
author:     tianchen                      # 作者
header-img:  img/4_1/city.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 心得
     - DL
---

# Training Techniques

> 在调试imagenet的时候试错代价实在是太大了，于是开始整理一下训练的一些技巧，来manual-ml

## Summary

|Dataset|Network|Acc.|(Source)|Epochs|BS|LR|Additional|
|--|--|--|--|--|--|--|--|
|ImageNet|ResNet18|69.5|[Pytorch example](https://github.com/pytorch/examples/blob/master/imagenet/main.py)|90|128|0.1-[30,60,90]x0.1| / |
|ImageNet|ResNet50|76.3|[Accurate, Large Minibatch SGD: Training ImageNet in 1 Hour](https://arxiv.org/abs/1706.02677)|90|512|0.5-[30,60,80]x0.1| / |




---

* Cifar10 Normalization: ```(0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010)```
* ImageNet Norm ```mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225])```

* [imagenet的pytorch标准training](https://github.com/pytorch/examples/blob/master/imagenet/main.py) - res18约69%，能tune到70
  * Lr Schedule (Batchsize 128) 0.1开始，decay by 0.1 every 30 epoch （overall 90 epoch）
    * 有一些文章说明了batch size和lr是成正比的，需要等比增加，也有人认为是一个平方关系
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211135930.png)
    * 同时momentum也可以根据batch size做对应的调整（1-momentum）反比于BN
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211125206.png)
  * 标准dataset pre-processing
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211135050.png)
* Test的时候每个epoch先acc大后acc小属于正常情况，这是因为test的时候没有shuffle

* [Google Brain的一篇文章](https://openreview.net/pdf?id=B1Yy1BxCZ) (Related Work在最后，而且很有用)
  * 表示batch size变大等价于lr decay
  * 说明了lr decay等价于simulated annealing
  * （然而这对于我们这种正常显存用户没用）
  * 实验中有一些setting
    * 一般training scheme (Cifar) 0.1开始(Batch size 128)，decay by 5,0.9 momentum
    * 对应的increased lr 0.5 (Batch size 512)，momentum 0.98
    * ResNet50 ImageNet 76.3% - [30,60,80] decay

* [Original ResNet Paper](https://arxiv.org/pdf/1512.03385.pdf)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211141302.png)
  * 初始lr 0.1，decay by 0.1 when error plateus, 1e-4 weight decay, 0.9 momentum
  * 有standard augmentation\

* 对Finetune，一般采用原先训练时候的最低lr
  * Cifar10 (160/40)
  * Finetune (90/20)
  * [Code](https://github.com/Eric-mingjie/rethinking-network-pruning/blob/master/cifar/l1-norm-pruning/main_E.py)
    * CIFAR10: bs 64, epoch 160, [0.5*160,0.75*160]decay by 0.1 (finetune 1e-3)
    * ImageNet: bs 256, epoch 90, every 30 epoch 0.1


## Pruning 

* Pruning from scratch引用了Rethinking的一个结论，就是prune model需要更长的训练时间(Cifar10从一般的160epoch到300)

* [Rethinking the Value Of Network Pruning](https://arxiv.org/pdf/1810.05270.pdf)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211151322.png)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211151541.png)

* [FPGM code](https://github.com/he-y/filter-pruning-geometric-median)
  * tune from pretrain用的lr是0.01
  * imagenet跑100个epoch lr-0.1，decay10 every30，batchsize 256
  * Cifar 300 epoch，bs-128，lr-0.1-[150,225]by0.1,

* [LeGR]()
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200213141659.png)


## Tricks

* [Label-Smoothing])(https://arxiv.org/pdf/1906.02629.pdf)
  * 发现了当teacher用label-smoothing来训练的时候，KD的效果没有之前好了
  * smoothing encourages the representations of training examples from the same class to group in tight clusters
  * 一般取0.1

