---
layout:     post                    # 使用的布局（不需要改）
title:      Self-Supervised Learning 自监督           # 标题 
subtitle:   LeCun　Says it's the future    #副标题
date:       2020-03-20           # 时间
author:     tianchen                      # 作者
header-img:  img/5_1/cat-0.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 综述
---

* NCE
  * 核心思想: 将真实样本和一系列“噪声样本”进行对比，从中发现真实样本的规律
    * 从一个其他分布中采样出Sample(来自N(x)分布的就是负样本)而来自原本分布P(x)的认为是正样本
    * 对于ML的Classification任务，就是只认为当前的样本以及其Aug为一个P(X)，其他的Dataaset中的东西都是来自N(x)
  * 说人话就是NCE是将其转化为一个二分类问题，真实样本为1，另外一个分布的采样样本为0



* [Data Efficient Image Recognitio with Contrastive Predictive Coding](https://arxiv.org/pdf/1905.09272v2.pdf) by DeepMind
  * 将Unsupervised领域的Contrastive Predictive Coding的方法改进(revisit)并且加入到Semi当中
  * 目前Semi的ImagenetSOTA / Also work on Det (PASCAL_VOC 2007)
  * CPC - Contrastive Predictive Coding
    * Learn Representation
  * Workflow
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200326142002.png)
    * 1. Unsupervised Spatial Prediction
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200326153411.png)
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200326153436.png)
      * 首先将图片分为多个有Overlapping的Patch，构成一个Grid
      * 经过一个前景Feature ectractor,最后接一个Mean-Pooling，保证每个Patch对应一个Feature vector
      * 之后再经一个Context Network，目的是Recognize出输入的Embedding(Feature Vector)是来自哪个Grid Cell
        * ContextVector输入的是一系列Zij(其感受野是所有在它之前(↖)的Zij，输出是一个context Vector cij
        * 这里训练中用到了InfoNCE Loss，改进自NCE(NoiseContrastiveEstimation)最大化cell以及其来自位置的mutual info，就是从Z_l中采样出一些z，只有zij match了才是正样本
        * Encoder和Context Network一起训练  
        * **这一步的核心思想是利用之前的信息去预测之后的信息，ContextVector-Cij中包含了ZiJ+的信息，但是其不是输入**
          * 以此去获得更加Consistent的Feature，就是Time(这里是Location-Invariant的Feature )
    * 2. ClassificationTask
      * 后面的ContextNetwork不要，直接利用已有的Label按照Supervised的方式接一个分类网络(甚至可以是一个简单的Logistic Regression)

* 上文的前身[Contrastive Predictive Coding](https://arxiv.org/abs/1807.03748)
  * [Post](https://mf1024.github.io/2019/05/27/contrastive-predictive-coding/)
  * Unsupervised Training Feature Extractor for Representation
  * 思想是利用Prediction作为目标(objective)
    * 常见的思想是Training the model to predict Future / Some Missing Information(对于上文来说就是Patch对应的Localization的Info)
      * 这个思想是否与VAE或者是GAN的思想类似？
    * 利用这个Prediction问题所提取的Encoder
  * 在采出的一个正样本和一系列负样本中，让预测结果去贴合正样本，最小化的是互信息量/或者是KL散度(类似这样的方式)
  * *这篇文章本身不止说Vision(还有NLP和RL)，上面那篇是只针对Visual*


* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200325231129.jpg)



* [MoCo](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1911.05722.pdf) by Kaiming
  * Contrastive Learning本身是LeCun在2006CVPR上提出的，思想就是拉近类内的，拉远类间的；训练的方式是NCE
  * 将Contrastive Learning的问题抽象为了一个Dictionary Learning的问题
    * 维持一个Dict其中有一些Encoding，当进入一个新的Sample的时候，经过这个Encoder，我需要它能从这个Dict中Query出一个与其最类似的Key，这样就有两个问题： 1. dict要足够大 2. Dict要具有一致性
    * Similarity是用InfoNCE(a kind of contrastive Loss Function)
      * 当Query接近正样本的时候，InfoLCE小
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200327145324.png)
      * tau是Temperature，k+为Key的正样本(理解是输入图像经过MomentumEncoder的结果)这个式子应该是在1个正样本和K个负样本上做一个Sum
    * Dict中的Keys是On-the-fly被定义出来的(Momentum Encoder 给出的结果)
  * 如何判定是不是同类别？InstanceDiscrimination:
    * 该Sample与其他的所有图片都是异类，对其本身Aug出的结果认为是同类
  * Inference的时候后面直接跟一个1层的fc，加一个softmax
  * 提到了CPC - CPC通过对多个时间点共享的信息进行编码来学习特征表达，同时丢弃局部信息。这些特征被称为“慢特征”
  * **有一个很牛逼的地方是将ContrastiveLearning问题抽象为了一个DictionaryLearning的问题，并且将之前的方法统一在了这个Framework当中**


* [S4L-Self-Supervised Semi-Supervised Learning](https://arxiv.org/pdf/1905.03670v2.pdf)
  * ICCV2019 - Google Brain
  * 是将一系列Self的方法套到Semi上来
  * 摘要里就提出了本文的insight是将Self-Supervised Representation Learning的一些方式引入到Semi—Supervised当中来，还指出和一些现有的Semi的方法可以Work in Parallel & Jointly Trained
  * Related Work中提到Self-Supervised Rely on Surrogate(Pretext Tasks) formulated with only unsupervised data
    * 早期的文章用一个CNN能够predict relative location,后期被拓展到学习MultipleRandomSampled Permuted Patches
    * 也会有人用GrayScale Image Colorization作为先决的Task，或者是predict angle
  * 除了一些用surrogate task的还有人在Representation space用一个Constraint
    * Examplar Loss，希望Representation对于heavy image augmentation invariant
    * 也有人给了一个限制: 所有图片Patch的Representation的Sum应该与全图的Representation一致
    * Altering between在Representation空间做Clustering和正常的model learning
  * Method首先给出了问题抽象
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200327153526.png)
    * W是参数，L_u按照具体任务Setting来定义
    1. S4L-Rotation
       * 在0/90/180/270中选择
       * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200327154203.png) 
    2. S4L-Examplar
       * 学习对Aug无关的Representation(和聪哥的工作很接近)


* [BigBiGAN - Large Scale Adversarial Representation Learning](https://arxiv.org/pdf/1907.02544v2.pdf)
  * DeepMind
  * 指出了近年来Self-Supervised的方法逐渐打败了基于Adversarial的，本文着手于用Adversarial learning来改进Representation
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200326155430.png)
  * 之前的工作BigGAN和ALI(Adversarial Learnt Inference)其flow一般为:
    * 我有Data X服从分布P_x，还对latent Variable Z有一个分布P_z(作为prior一般是gaussian)，Generator建模P(X|Z)-也就是从分布P_z中采样出Latent Variable Z，然后还原回X；对于传统的GAN，有一个Discriminator(Encoder)建模相反的概率分布P(Z|X),给定数据，predict这个embedding(\epsilon)
    * BiGAN(bidirectional)加入了一个Joint Discriminator,输入的是一个(X,Z)的Pair，需要分辨的是它们是来自Sample&Encoding还是LatentSample&Generator
      * 这样看来Generator的目的是“Fool”Disciminator by 让两个联合分布更加接近P_x\epsilon & P_zG（两者之间的距离用了一个Jenson-Shannon Divergence）
    * 一个有趣的训练结果是，当G/D都是确定的函数的时候(两个分布是狄拉克分布的时候)它们两个在Global Optimal点上是互为相反值
  * 本文发现修改了Discriminator能够在不compromiseGenerator的情况下提升性能
    * Disciminator有三个组件FHJ，F的输入只为X，J的输入只为Z
  * 实际做分类的时候是先Unlabel的训练一个BigBiGAN，然后Freeze Representation，训练一个Linear Classifier在有label的数据上展开 


### Semi-Supervised

* [FixMatch](https://arxiv.org/pdf/2001.07685v1.pdf)


* [MixMatch]()
  * 用Autoencoder来重建输入图像，来获得好的Representation(或者是用GAN)
  * Semi较为有效的几个方案
    1. Consistency Regularization
    2. Entropy minimize： 来源于共识，决策边界不应该穿过边沿分布的高密度区域(Push Back Decision Boundary)吸引对未标记数给出低熵的预测
       * 对当前的结果肯定，所以有人用带Temperature的CrossEntropy 
    3. 最基础的L2正则化(在SGD下等价于Weight Decay)
       * 有说法说Adam和L2正则一起作用会出现区别
    4. 一种新方法是MixUp，任意抽取两个样本，构造混合样本和标签
       * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200321202307.png) 
    5. MixMatch

* [En-AET]()
  * 用一个VAE去找到最好的transform组合
    * encoder找到一个好的embedding，decoder re-parameterize出transform

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200321203810.png)