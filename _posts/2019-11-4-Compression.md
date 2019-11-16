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
  * *那么这个排序的过程的多余的支出有没有算进去呢*


* [Channel Prunning](https://arxiv.org/abs/1707.06168v2)
  * 起初的一篇文章,旷视
  * ICLR 2017

* [ThiNet](https://arxiv.org/pdf/1707.06342.pdf)
* SJTU    

* [Rethinking the value of network pruning](https://arxiv.org/pdf/1810.05270.pdf)
  * ICLR 2019
  * 通过对比直接对修建之后的模型train from scratch，比先训练再finetune起到了相当甚至更好的效果
  * 证明了首先训练一个over-param的大模型是不必要的,其实剪枝真正起效果是在**学习到了有价值的网络结构**,而*学到的那些参数其实用处不大*
  * 等于说用NAS把剪枝的命革了


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


---

# 飞哥的定点工具
* 主要库 ```import nics_fix_pt as nfp```
* nnf就等价与nn
 * ```import nics_fix_pt.nn_fix as nnf```
* 对每个model要做一个```model.set_fix_method(fix_method)```
 * fix method有三种
   * FIX_AUTO/ FIX_FIX / FIX_NONE
* 这样定义model
 * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191105211057.png)
* ```net.fc1_fix_params['weight']['method']```
  * 这一步操作在model.train()之前
  * **这个东西是要作为nnf.Linear(nf_fix_params)加入的**
  * 两层都是dict,里面存着Config的信息
  * 第一层就是generate config里的names,也就是weight/bias以及其他
* ```net.print_fix_config()```
  * 一次性打印下所有的config  

---

* 如果已经有了网络结构，可以通过```from nics_fix_pt import register_fix_module```来获得定点
