---
layout:     post                    # 使用的布局（不需要改）
title:      从VAE到Diffusion # 标题 
subtitle:   personal notes about generative model #副标题
date:       2023-03-29            # 时间
author:     tianchen                      # 作者
header-img:  img/2022_0710/5.png   #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - survey
---

> 记录梳理一下自己学习了解generative model的一系列笔记，合并在这里，for further reference

首先我们回顾一下贝叶斯估计,其中$$P(B)$$为Evidence，$$P(A)$$为Prior，而$$P(B\|A)$$是likelyhood，$$P(B\|A)$$为Posterior。我们希望能够最大化后验概率，用含参模型去拟合likelyhood函数。


$$P(A|B)=\frac{P(B|A)*P(A)}{P(B)}$$

* ![equation](https://latex.codecogs.com/svg.latex?P(A\|B)=\frac{P(B\|A)*P(A)}{P(B)})


# References

- [Tutorial - What is a variational autoencoder?](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)