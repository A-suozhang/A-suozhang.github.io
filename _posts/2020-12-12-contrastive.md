---
layout:     post                    # 使用的布局（不需要改）
title:      Contrastive Learning            # 标题 
subtitle:   怎么感觉好像曾经写过… 
date:       2020-12-12            # 时间
author:     tianchen                      # 作者
header-img:  img/2020/street3.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - survey
---

# Contrastive learning

> ref the [Blog-ContrastiveSelfSupervisedLearning](https://ankeshanand.com/blog/2020/01/26/contrative-self-supervised-learning.html)这个post写的非常好…有机会要向他学习，目前自己的post确实都是随手记的

- The Gelato's Bet

unsupervised method could beat R-CNN on PASCAL by 2015 (finally recently MoCo achieve that)

- Self-Supervised Learning

1. use the data itself as the supervised signal
	* the underlying data distribution has richer information than sparse labels
	* for high-dimensional problems(such as RL), require label is harder
2. Main flow: Generative / Predictive / Contrastive
![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201212185232.png)
traditional generative methods, focusing on the reconstruction error on pixel space, not abstract latent space
3. Cur self-supvesied method on unlabeled imagenet + linear classifier could outperform AlexNet(Data-efficient CPC 2019) * Contrastive training on ImageNet beats pre-training from ImageNet

- Contrastive Learning 

Key: Score(f(x),f(x^+)) >> Score(f(x),f(x^-));
x is the `anchor point`
![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201212190040.png)
the denominator(分母) contains 1 positive & N-1 negative values, dot product as score function, N-way softmax classifier
called `infoNCE Loss`, `multi-class n-pair loss` or `ranking-based NCE`

* Scheme

4 Main Component: Data aug -> encoder -> Pretext Task -> Contrastive Learning

Moco将其抽象为了一个dictionary lookup的问题,归类了几种Scheme
1. 两个encoder，分别产生Positive和Negative的样本;grad同时回流两个encoder
2. 单独encoder处理pos样本，neg样本从一个memory bank中取得
3. 一个encoder和另外一个momentum encoder对encoder做temporal ensemble来处理neg sample

* Ideas:
1. 不去pixel-level的重构数据，是因为实际的feature可能更加高层。
2. InfoNCE其实是在用互信息量来衡量特征的好坏
3. MoCo的想法是增加负样本的个数，同时不一味增加memory bank

### Papers

- [DIM-Deep InfoMax-2018]()

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201212190419.png)
the contrastive task: classify wheter a pair of global and local feature are from the same image or not 
f(x) as anchor - global feature: output of the decoder of CNN representation
f(x+), f(x-) local feature: intermediate output of the CNN encoder, if from the same image, f(x+)
many follow-up works on graph & RL, also augment multi-scale

- [CPC - Contrastive Predictive Coding]()

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201212190904.png)
could apply on any form of data(view it as a seq)
focus on learning the `slow feature`, which don't change quickly along time, while discarding the `local representation`
contrastive task: {x1,...,xn} a seq of data, xt is the `anchor point`, x{t+k} is the postive sample(usually k>1 as the fig shows), x{t\*} randomly sampled from the seq is the neg sample 
an auto-regressive encoder network is applied to encode the historical content

- [Learning Invariances with Contrastive Coding]()

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201212192146.png)

focus on using contrastive learning to improve invariance in feature space(which is advantage of the CL), if we want features that are invariant to transformation T(x), for anchor data x, T(x) is positive sample & T(x') is negative sample, AMDIM & CMC focus on this scheme

- [Moco]()

> Sclaing the number of the negative sample

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201212192801.png)

(note that grad donot flow back through the momentum encoder of MoCo)
Insights: CL works better with more negative samples, usual form of CL, the grad flow back through encoders of both positve/negative samples, so the negative sample is restricted to size of batch size.
Moco(Momentum Contrast) solve this by maintaining a queue of negative samples, no using the grad to update the negative encoder, preiodically update the negative encoder with momentum update.
(the postive sample forward through the encoder, and the neg sample forward throught the momentum encoder, generating the loss, the grad backward through the encoder. then the momentum encoder is weighted sun of the encoder) 

---

* V2

参考SimCLR，更新了非线性层以及优化了数据增强方法

- [SimCLR - NIPS2020]()

更大的batch-size
提出在encoder后加一个额外的非线性映射 W(ReLU(Whi)),发现encoder的结果会保留与数据增强相关的信息，用一个非线性层去掉这些信息(该层只在无监督训练中使用)
探索并优化了数据增强方法的组合方式

* V2

也参考moco加上了momentum，以及换arch

- [SwAV - NIPS2020 - (FAIR)]()

motivation: 对比学习需要neg sample进行比较，消耗计算时间以及内存
key: 首先对各类样本进行聚类，然后区分每类的簇
![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201213110456.png)
对于一张图片，encoder产生了embedding z,定义了K个聚类中心C，Z与C结合产生最终的embedding Q，Q的值表示第K个聚类中心与第b个样本的相似度(Loss也是取的内积)
此外提出了一种新的数据增强的方法，对不同分辨率的view进行mix


- [BYOL - DeepMind]()

Motivation: 不使用对比这一scheme,Representation Learning 解构为用一种view去预测图形的其他view，这样就可能会`collapse`,全部收敛到某一种表示，自己去预测自己,Contrastive Learning将预测的问题转化为了对比的问题，强迫模型输出的多样性，避免了坍缩,BYOL尝试不用对比(Neg Sample),公用encoder，将MSE作为损失，缩小不同view之间的距离，会引发坍缩。但是如果将其中一个encoder固定下，可以一定程度上避免坍缩。后续参考momentum的思想改进

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201213112341.png)

上侧Online，含有梯度更新，下侧是Target，不去更新梯度。优化的目标是用Online去预测target的表征(MSE Loss)。Target是online部分encoder的滑动平均,最后预测的时候只用online部分(好像MeanTeacher的架构)
后续的工作发现这个MLP中的BN非常关键，BN在显示的做对比，除去batch内相同的部分
不依赖负样本之后，其对数据增强就更加鲁棒了

- [SimSam]()

> Kaiming针对孪生网络的follow-up work

follow了BYOL的思路，找到了collapse的避免方法是stop-gradient
左侧的encoder产生z1，经过MLP之后输出p1，右侧生成z2，计算p1与z2的Cosine Similarity，左右调换，计算p2与z1的Cosine Similarity，并且最大化以上两个余弦距离的和，右侧encoder一直不回传梯度
为什么会work，参考[Andy's Blog](http://link.zhihu.com/?target=https%3A//mp.weixin.qq.com/s/-Vtl_8nND7WCPLdL5bNlMw)

