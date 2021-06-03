---
layout:     post                    # 使用的布局（不需要改）
title:      Note Stack 笔记堆栈           # 标题 
subtitle:   无处安放的笔记   #副标题
date:       2020-08-06            # 时间
author:     tianchen                      # 作者
header-img:  img/2021_0603/4.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 心得
     - 常用
     - 论文阅读
     - 综述
---
# Paper Stack 文章堆栈

> 由于文章stack实在过于混乱，更新且将该post改写为笔记的stack，文章的stack将在WhatIveRead的papers当中的stack.md进行更新

### 2020-03-17 MCMC

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200317151113.jpg)
* 撒豆子算面积的实验

### 2020-03-20 DeConv
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200320185002.png)

### 2020-03-24 Gumble Softmax
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200324113027.png)
* 从VAE(连续分布采样的求导)，加入一个Gumble噪声(包含了参数的信息)，首先从Gaussian中采样，带入值
* 对于离散分布，加一个Argmax->Softmax 

### 2020-04-05

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200405093121.jpg)


### 2021-01-03 NES

* Keys

a family of black-box optimization algorithms use natural grad to update the parameterized distribution in the direction of higher expected **fitness**

* Source - Jurgen 

* Motivation

* Methodoly

Iteratively update the searched distribution by using estimated grad on its distribution param(e.g. \miu & \sigma of gaussian), iter stops while the criterion is met.

1. Search Gradient method

> what is search space? what is fitness?

however, to locate a quadratic optimum, should be at least quatratic, since 1-order methods will be unstable
the update is not scale-invariant
this does not occur in general grad-based case, since here the grad controls both the position and the variance of distribution over the same search space dimension
this problem is solved with the `natural gradient`

2. Natural gradient

> how to make it a constraint optimization

natural gradient is proposed in the ml field to help mitigate the slow convergence in plataeu landscape / ridges(山岭，山脊)
the plain grad \delta{J} represent the steepest ascent(in the space of the actual param \theta)
when the learning-step \epsilon is small, the problem could be reformed as finding a new distribution with param chosen from the 
hypersphere of radius \epsilon and center \theta that maximize the plain grad \delta{J}. so the Enculidean distance is used for 
measuring the distance between the distribution, so the update is dependent of the parameterization of the distribution. 
the key of natural is to remove this dependence, finding a natural distance*(e.g. KL distance), reduce it to the constraint optimizaiton
***use the natural grad instead of the steepest grad for optimization*

3. Fitness shaping - make the distribution invariante to (arbitary but order-preserving transformation)

> what is order? simply make the ranking of the fiteness function of the population no change?

not so crucial.

4. Adaptive sampling - adjust the lr online

meta-learning based(sample new \theta, if quality significantly better, continue with \theta_hat), apply hill-climbing and the Mann-Witney U-Test

5. rotation-invariant distribution

local-natural coordinate
sampling from radical distribution


* Experiment

* Ideas

multi-variant gaussian, fisher information matrix

- closely related to CMA-ES(Convariance Matrix Adaptation Evolutional Strategy)



