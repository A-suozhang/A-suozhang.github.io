---
layout:     post                    # 使用的布局（不需要改）
title:      Compression相关的一些内容...          # 标题 
subtitle:   将来吹牛逼可能会用到?...   #副标题
date:       2019-11-5           # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/ucentre2.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 综述
---


> [VALSE 2018年度进展报告 | 深度神经网络加速与压缩 - 计算机视觉life的文章 - 知乎](https://zhuanlan.zhihu.com/p/36616989)

# All About Compressions

### Background
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191104212124.png)
  * (~~需要找一张更好的图~~)

### Low-Rank
* 一些数学上的矩阵低秩相关的
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191104212319.png)
* 后续做的人少了,一个是需要前景的大量计算,一个趋势是Kernel越来越小,不是很Compatible

### Prunning
* 早期的随机剪枝(Dropout)对硬件很不友好
* 目前主要基于*结构化,滤波,梯度*等
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191104212516.png)
* 组稀疏化
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191104212701.png)
* 给FeatureMap记录下尺度因子
  * 直接按照FeatureMap进行Prunning
  * 也是一种Channel Prunning了 (~~一剪剪一片~~)
* 对Training中引入Prunning
  * 对梯度更新的时候进行了分析,发现只需要对所有的梯度的一部分就可以达到之前的效果
  * *那么这个排序的过程的多余的支出有没有算进去呢>*

### Quantize
* 目前最热的其实是DataFreeQ
  * (我目前还是没有特别理解)
* 一些基础操作


### 未来方向(当年的,也就是现在的)
* Non-finetune Compression (Unsupervised)
  * 不需要带标签时候的压缩
  * *可能是我在尝试的?*
*  Self-Adapt的Compression
   *  *这个问题好像现在在NAS*
*  hardware-Co-Design
*  Other Tasks