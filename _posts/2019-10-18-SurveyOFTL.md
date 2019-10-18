---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读《Deep Visual Domain Adaptation: A Survey》          # 标题 
subtitle:   又一个简单暴力的TL   #副标题
date:       2019-10-18            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-xueyuanroad0.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
     - 迁移学习
---

# [Deep Visual Domain Adaptation: A Survey](https://arxiv.org/abs/1802.03601.pdf)

* 2018(~~后面出来的一些模型基本上也不太好实现~~) 北邮
* 面对的场景:好的标注数据仍然不是很够
* 存在问题：Distribution change (Domain Shift)-会显著降低NN的性能
    * DA(Domain Adaptation) From *Source* To *Target*
* 分类
    * 粗分为两种
        1. Instance-Based
            * Reweight The Target Samples,and Train On It
        2. Feature-Based
            * 学习一个Shared Space,两个Dataset在该空间上是匹配的
                * 这里怎么个匹配法就是玩法了（很多都基于一些数学的性质比如Wasserstein距离，以及协方差（CORAL））
    * 经典Survey分为3种
        1. Inductive
        2. Transductive
        3. Unsupervised
    * 由于年代相对早，均考虑的是同质(homogenous)特征空间
        * 后续还有人研究heterogenous TL 
        * 两者的区别是，同质的只考虑了Distribution Shift，而异质的考虑当Feature Space也不一样的时候
        * 所以同质的时候Feature Space相同且with The Same Dimension(?)
        * 这其中，根据Data Accessible 的程度不同，分为Supervised,Semi-Supervised,以及Unsupervised
    * 我们一般默认Source和Target Domain是直接Related的，这样只需要一个
        * One-Step DA
        * Multi-Step DA

    * 这些年逐渐从ML的方法（Shallow TL）比如SVM等，相对数理的方法，到全面开始炼丹(Deep TL)
* Methods
    * 广义的Deep DA
        * 真正做的Knowledge Transfer的其实是Shallow Method（还是ML的那一套东西），NN其实只是做一个Feature Extraction或者说是做Representative Learning
    * 狭义的Deep DA
        * 都靠NN来干，embed DA Into The Learning Of The Representation
        * （端到端，更加适合我们的实际操作方式）
        * *3 Cases* 
            * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016205545.png)
            * 1. Discrepancy（矛盾）-Based：
                * 假设通过Finetune能够缓缓的消除两个Domain之间的Shift
                * 有4种操作方式
                    * Class-Criterion: Use Class Label Information As Guide
                        * *需要在Taeget Domain中有少量的数据Label*主要是利用Pretrain模型对这些Label的结果，来修正模型来达到Transfer的效果
                        * 当有足够多的Label的时候*Soft-Label*&*Metric Learning*
                        * 当没有的时候，借助Pseudo Label
                    * Statistics-Criterion:从数据的角度对齐两域的分布
                        * *MMD*&*CORAL*&*KL Divergence*
                    * Architecture-Criterion：通过修改网络的结构来学习到更Transferable的Feature
                        * *Adaptive BatchNorm*&*Domain Guided Dropout*
                    * Geometric-Based：更偏向从数据本身入手？
            * 2. Adversal-Based（对抗）
                * 构建一个Discriminator去判断数据是从Target还是Source Domain中来的，通过尝试去骗过这个Discrimnator，来Minimize两个Domain之间的区别(有一点抽象了哈)
            * 3. Reconstruction-Based
                * 通过重建数据来保证DA的有效性（这个假设真的成立吗？？？）
                * 这样可以保证确实获得了Intra-domain的Representation
                * 大部分基于*Encoder-Decoder*或者是*GAN*
            * *近几年的文章基本是靠后的，但是我们现在的情况还是更倾向于第一种的前两种Sub(相对比较Feasible一些)*
    * Discrepancy-Based中的比较可行的前两种
        * Class-Criterion:
            * *还是需要一些Task Domain的Label的*
            * 基本操作 (有一些是有点类似是一些Finetune的技巧了)
                1. 把新Label的GT与Pre-train Model的Softmax做为Loss训一训
                    * 有的时候用到了Hinton的Soft Label（也就是KD中的Softmax with Temperature）
                2. Metric Learning
                    * Semantic Alignment Loss
                    * Separation Loss
                    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016213039.png)
                3. Attribute Classification 
                    * 从High Level semantic attribute入手
                    * (?) Didn't Throughly Understood (On Page 6)
                    * [29] 基于Attribute做了Fine-Grain Recognition
                4. Pseudo Label
                    * 以最大后验概率的原则估计出假Label
            * 思路（很明确）
                * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016212718.png)
            * 感觉上和我们最需要的还是不是特别一致
        * Statistic-Criterion
            * 一般从分布的角度入手，专注与*学习到与Domain无关的Representation*
            * MMD是一个很关键的Metric（具体定义还挺数学的，不过应该是挺普适的一个东西）有着十分深厚的数理基础，第7页还有对这个东西的数学解释，没看
                * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016214234.png)
                * 本质上是通过一个Kernel function将原本Data映射到一个RKHS(Reproduced Kernel Hilbert Space)
                    * *DAN*，*JAN*,*RTN(Residue Transfer Networks)*
            * ⭐COROL则是学习一个（非）线性变换，将第二个Domain转化到第一个Domain
                * (相对来说最Feasible的一个了)
        * Architecture
            * Related Weight很直接很暴力
                * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016215626.png)
            * Adaptive BN:
                * [69] ⌛⭐认为Weight包含了区分Class的信息，而BacthNorm的信息包含了domain相关的信息，针对Target Domain做Adpative的BN，多加了一些Alignmnet params（BN中同时包含了原有的分布和新Domain的分布）
                * 后续人们发现当做了Instance Normalization之后，性能可以进一步提升
            * Domain Dropout
* 几种训练的情况
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016212028.png)
* 巨大的Performance表格
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016220901.png)
* [一些比较有用的开源代码实现](https://github.com/jindongwang/transferlearning/tree/1938dff2a5085f1fb0c59fdb86d65045520ae72a/code/deep)
