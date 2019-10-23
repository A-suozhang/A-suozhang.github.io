---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读《Accelerating Deep Unsupervised Domain Adaptation with Transfer Channel Pruning》          # 标题 
subtitle:   是不是我的所有idea都被人想过了呢...   #副标题
date:       2019-10-23            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-ucentre1.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
     - 迁移学习
---


# [Accelerating Deep Unsupervised Domain Adaptation with Transfer Channel Pruning](https://arxiv.org/pdf/1904.02654.pdf)
* 首先是一种UDA(Unsupervised Domain Adaptation),而且是TCP(Transfer Channel Prunnning)
* 测试数据集Office31&ImageCLEF
* 目前的Transfer办法都没有部署Resource Constrained场景
  * 表示能够同时完成Prunning和迁移训练
  * 还说因为自己考虑了Distribution Discrepancy就可以去避免Negative Transfer
* 传统的Compression方法不能用,以为
  1. 其场景是Supervised Learning,需要Label去Finetune,但是我们的场景没有
  2. 传统的方式没有考虑Distribution Discrepancy,会导致Negative Transfer
     * AdaBN的方法是否有效?  

### Related Works
* UDA(无监督自适应迁移)
    1. Subspace Learning: 找到一个两个Domain都适用的特殊特征空间
       * SDA(Subspace Distribution Alignment) 直接对齐Vevtor
       * CORAL:对齐的是特征的二阶统计量(协方差)
    2. Distribution Alignment
       * JDA(Joint Distribution Alignment)
         * 去Map到同样的数据分布
       * 后来JDA还被改进到使用Domain Invariant Clustering
       * 还有一些很数学很玄学的Manifold Embedded Distribution
    3. NN嵌入到以上的方法中
* Channel Prunning
  * 更fast&efficient
  * 实际上没有引入稀疏性,也不需要额外的架构
  * 作者认为剪去的是对两个Domain都不重要的,需要经过一个*Transfer Channel Evaluation*的过程    


### Implemention
* 两个Domain需要有相同的Label Space(也就是解决的问题是一样的,分类的类别是一样的)
1. 首先正常训练并用正常的UDA方法Finetune
   * 主体是一个DAN
   * 需要计算MMD(Maximum Mean Discrepancys)\
   * 而DAN的Backbone可以是VGG或者是Res 
2. 然后对卷积层做Transfer Channel evaluation做剪枝Finetunes
   * ~~原本Channel Prunning的指标,贪心地去寻找*使Loss变化最小的Channel*~~
   * 泰勒展开舍弃余项
     * 最后化简到 ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191023203326.png),需要找这个值
     * Train-Flow 
       * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191023203532.png)
       * 这个贪心很烦!
3. iter,直到精度和模型大小满足TradeOFF


### Experiment
* 对比了3个Scenario
   1. 传统的2-Stage方法
   2. 不加MMD-Loss的训练
   3. 完整的 
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191023204410.png)
  
### [Code](https://github.com/jindongwang/transferlearning/tree/master/code/deep/TCP)
* (待分析)
* ⌛

### 🤔THINK
* 拿AdaBN过来到底怎么样(但是ADABN不是UDA呀!fintune得拿东西过来tune啊)