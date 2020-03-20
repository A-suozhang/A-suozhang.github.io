---
layout:     post                    # 使用的布局（不需要改）
title:      Paper Stack 文章堆栈           # 标题 
subtitle:   待读的文章和无处安放的文章解读   #副标题
date:       2020-03-06            # 时间
author:     tianchen                      # 作者
header-img:  img/1_28/nantong-7.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 心得
---

# 石墨文档List

* [GATES Related](https://shimo.im/sheets/T3Cp36PRHPj38TKp/MODOC)
* [NAS Reading List](https://shimo.im/sheets/T3Cp36PRHPj38TKp/MODOC)



# Paper Stack 文章堆栈

### GNN

* Foundation

### NAS

* [Transfer Learning with Neural AutoML](https://arxiv.org/pdf/1803.02780.pdf)
  * Parallel Training on Multi-tasks, Transfer the search strategy
  * 当有了一个pretrained的controller的时候，只有task embedding需要更新
  * Generic SS (Learned task representation & Task-specific advantage normalization)  
  * In standard single-task training of NAS, only the embedding
of the previous action is fed into the RNN. In multitask training, the task embedding is concatenated
to the action embedding. We also add a skip connection across the RNN cell to ease the learning
of action marginal distributions

* [MetAdapt: Meta-Learned Task-Adaptive Architecture for Few-Shot Classification](https://arxiv.org/pdf/1912.00412.pdf)
  * 用MetaLearning+NAS来搜索出更适合Few-shot的架构
  * Differentiable-NAS
  * 发现FewShotLearning的backbone model是先越大越好，然后会差
    * 认为D-NAS可以mitigate overfitting
    * Follow DARTS - Arch as DAG
  * 与一半NAS不同，不是找到一个架构用
    * 找到能更快adapt到别的task的
    * 有一系列small networks,MetAdapt Controller，同时更新weights，修改connection
  * FewShotLearning
    * Metric Learning based
      * Learn a non-linear embedding into a metric space, use L2 distance for classification
        * (Some change L2 disatnce with a implicit learned function/GNN)
      * Could improve when adding semantic information
    * Generative Based
      * 或者说是augmentation-based
      * 设计到一些transfer相关的一些东西
    * Meta Learning - learn a learning stragegy easily adapted to other few-shot tasks
      * trained on a set of few-shot tasks (episodes)
      * 另外一支是gradient-based
        * MAML
        * MetaSGD
    * MetAdapt的flow
      * arch as DAG , feature map as node / op as edge
      * Task-Adaptive Block
* 这个图的配色不错
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200313202441.png)

* [Channel Gating Neural Networks](https://papers.nips.cc/paper/8464-channel-gating-neural-networks.pdf)
* 思想来源于Feature Map中有很多绝对值小的地方，这些东西其实是可以不需要的
* 核心观点在于，认为partial sum和global sum是有相关性的(成立的前提假设-做了实验一半的channel有0.86的相关性)，所以partial sum发现不effective的部分没有必要继续做计算(以一定condition)
  * 这个threshold是硬的
  * 可以fine-grain也可以channel-wise
  * 把所有的W_l分一部分W_p和另外的W_r,它们的结果应该在这一层是要加起来的，把W_p* X_p的输出(Partial Sum)输入一个Channel Gating模块，以一个threshold来确定是否需要对剩下的W_r Apply Mask，给出一个Out_C维度的Mask，选择的依据是Feature Map的Activation绝对值
  * 相当于是一个新的layer type
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200313154531.png)
* 有一个channel grouping确保all channels are treated equally
  * 相当于防止每次都把前几个channel做为W_r，所以加了一个channel shuffling
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200313162432.png) 


* [Review Netowrk Pruning via Transformable NAS]()
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200317204835.png)
  * 用NAS来处理Channel Pruning的问题
  * 每个layer的channels，首先需要设计一个distribution，用类softmax的方式来，决定选择每个channel的概率
    * 选取channel的过程本身不differntiable，用gumble-softmax来
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200317203915.png)
    * CWI (Channel-Wise Interpolation) -  Adaptive Average Pooling
  

* [HAQ: Hardware-Aware Automated Quantization with Mixed Precision](http://openaccess.thecvf.com/content_CVPR_2019/papers/Wang_HAQ_Hardware-Aware_Automated_Quantization_With_Mixed_Precision_CVPR_2019_paper.pdf)


* [NAO]
* [Semi-Supervised NAS]

---

* [MAML - Model Agnostic Meta-Learning for Fast Adaptation]()
  * Learn2Learn
  * 对标的是最经典的从pretrain model的transfer learning (严格来说Fintune也是一种Meta-Learning)
  * MAML还关注着多任务(与MultiTaskLearning有许多的关联)
    * 比如人脸关键点和定位是同样的一个问题，但是对NN来说是作为两个问题来处理的
    * MAML是以一个Task为基本单位的
  * 最终目的是**学一个好的初始化参数**
  * 新的Loss Function
    * 一部分loss是经典的loss，另外一个部分其他任务的损失参数
    * 有一个思想是不找单个任务的全局最优(Global Minima)，因为这样可能很难收敛到别的task的好结果，而是找到多个task之间的一个平衡(也不用最好)
    * 而传统的finetune首先是从任务A的最佳解出发，所以不一定对第二个任务效果好
  * 操作
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200317213623.png)
    * 我们先有了随机初始化的网络theta_0,在第一个task上按照正常的sgd训练获得参数theta_m,然后与此为基础获得valid loss，并且用这个梯度，不去优化theta_m而是去优化theta_0,去得到theta_1.下面再以theta_1作为上一步的theta_0，去apply到下一个任务（*这样去经历A String Of Tasks-有点像一系列课程*）


### 2020-03-17 MCMC

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200317151113.jpg)
* 撒豆子算面积的实验


### 2020-03-20 DeConv
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200320185002.png)



