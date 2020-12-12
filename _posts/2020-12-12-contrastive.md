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
