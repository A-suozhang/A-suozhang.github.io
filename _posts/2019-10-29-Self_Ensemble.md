---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读《Self-ensembling for visual domain adaptation》          # 标题 
subtitle:   又一个自己的idea...   #副标题
date:       2019-10-28            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-nmb4.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
---

# [Self-ensembling for visual domain adaptation](https://arxiv.org/pdf/1706.05208.pdf)

* 结合了Mean-Teacher和Temporal的ensemble方式
* VisDA2017 - ICLR2018
* Semi-Supervised方法的意义，减少GT Label的要求（减少人工成本）
* 从Synthetic Data到Real Data
* Confidence Thresholding & Class Balancing


## Related Work

### Semi & Self-Ensembling
* []() 提出了两种Self-Ensemble的方法
  * Pi Model： 每个Unlabel数据过两遍，每次经过不同的Dropout（等数据增强） 
    * 有一个Unsupervised的loss1是两者推断的一个Squared Difference
  * Temporal Model: 通过一个滑动平均来鼓励模型prediction与平均一致
    * 滑动平均就相当于对各个模型做了一个Ensemble
    * 有一个改进：不是对prediction而是对Weight来做
      * 有一个TCH，STU
      * TCH的weight是STU的moving average
      * Unsupervised Loss 是TCH和STU的mean square difference
        * 给TCH和STU是经过不同的dropout，noise以及数据增强的
        * （面对的是Semi的问题，而不是DA）

### Other Way Of DA

* AutoEncoder
  * 将两个域的图片都Encode进了一个Latent Space
  * 做法是Split Model，对Representation分两部分；1. Shared Feture & Domain Sprcific
  * 让Classifier只利用Domain Invariant的Feature去predict Source Label
* Bifurcated（分支）
  * 训练一个classifier尝试去区分Target来自哪个Domain(有点类似于Incremental)
  * gradient reversal layer - encourage the feature extractor to 
* Tri-Training
* GAN
  * 利用其中的Generator去完成sample从一个到另一个Sample的Transform
* 对齐Feature Distribution
  * Deep Coral
  * AdaBN

## Implementation

### Mean-Teacher

* Based on **Mean-Teacher Semi-Supervised Learning Model**
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191029203414.png)
* Teacher的Weight是Student的一个Moving Average
* 每个Sample都会同时经过Student和Teacher，给出不同的结果
  * 每个Sample会经过不同的Dropout以及数据增强方法

* 对于一个Batch
  * 同时含有Labeled以及Unlabelled Sample
  * Loss有两个部分Supervised & Unsupervised Loss
    * Supervised的部分就是student Prediction与GT的交叉熵
      * 对Unlabel Sample来说是0
    * Unsupervised 部分是Self-Ensemble Loss
      * 惩罚了Stu & Tch的对统一图片给出的差距
      * Mean-Least-Square

* 有人表示这种flow的loss需要一个Time-Dependent的Weighting，不然可能会卡住
  * 操作是对前80个epoch的Loss做一个gauss[0,1]的相乘
  
### Adapt To Domain

* 改成，对Source Domain的数据用简单的交叉熵
  * 但是对target sample用Self-Ensembling
* Semi的Case中，Unlabel以及label Data是MIx起来的
  * 但是在UDA的问题中不能这么用，因为两个Domain有较为Distinctive的显著区别
* 实际上抽取Batch的时候是分别抽取的     
  * 所以BN能够根据不同的Domain更新BN里的统计参数
  * 与AdaBN有区别在于没有将source和target完全separate开来
* 并没有用2-Stage的Training方式
* 用**Confidence Thresholding**来代替那个不那么elegant的Gaussian Ramp Up，去达成*稳定训练*的效果
  * 如果做出的判断结果Confidence不是特别高，那么就把Self-ensemble Loss置(Mask)为0
  * 如果判别本身就比较模糊，那么不考虑Self-Ensemble
* **Data Augmentation**
  * Minimal： 垫一个0.1的高斯噪声
  * Standard：在[-2,2]的区间加translation和加horizontal flip  
  * Affine： 加上一个随机Affine Transform
* **Class Balance Loss**
  * 在loss上加一个Term来惩罚Prediction过于偏向某一类别的情况
  * 惩罚项是class Predictin和一个uniform的 Vector
  * 作者注意这个Term和Self-Ensemble Loss的balance，会对class balance loss乘一个系数
    * 这个下字数是一个Batch中有多少Sample Pass了Confidence Threshold
  * 这个思想和*Entropy Maximization Loss*有一些接近
  * 作者也思考到了如果Target Domain具有Class Imbalance，那这个方法反而会不work
* 两个Term理解为一个让Source的数据对 - 具有基本的数据分辨能力,或者说是学习到Domain-Invariant的Feature和分辨
  * 并且只在Target上尝试获得一个Consistent的模型(比较好的分类器)
  * 
* 其实思路和原本只用在Semi上的Mean-Teacher很类似,只是将两个域隔离开来(毕竟我们无所谓Source上的Performance如何)


### Expermient

### What To Modify
* 尝试去做Prunning,思路和TCP;类似,边训练边Prunning
  * 看用什么loss,可以做一做实验
* 
