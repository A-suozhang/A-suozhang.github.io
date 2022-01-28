---
layout:     post                    # 使用的布局（不需要改）
title:      与Batch相关的一些思路          # 标题 
subtitle:   All About The Batch   #副标题
date:       2019-10-20            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-dorm0.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
---


> 刷知乎看到了一篇[文章](https://zhuanlan.zhihu.com/p/86529347?utm_source=qq&utm_medium=social&utm_oi=60160827457536)，拿出来记录一下

# Things About Batch

* 除了BN之外，Batch大小决定了深度学习训练过程中每个iter之间的平滑程度
* 默认Backprop传的梯度是每个Mini-Batch中的Sample的Loss做了平均之后的梯度
    * 所以如果MinibBatch过于小的话梯度震荡会比较严重，可能*不收敛*
    * 如果MiniBatch过大的话，梯度变化就小，可能卡在*局部最优*
* 此外BatchSize的大小也一定程度上决定了训练的时间
    * 因为输入数据有一个维度是BatchSize，比如64个Sample分4次输入和一次性输入，所能获得硬件优化并行度不一（其实就是CUDA层面）
    * 所以如果说显存非常大的话，可以Load一个超大的MiniBatch
        * 为了防止梯度变化小，我们可以一次正向做多次反向传播，节约的其实是正向推断时候的计算速度
        * （然而实际计算中BackProp所花的时间大大多于正向）上面的方法理论上和BatchSize/（backprop）次数等价？

## Batch Norm

* 被认为能够提高精度，而且可以更快收敛
* 训练中用Batch内的均值和方差，训练后用整个Training Set的Global均值和方差
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191020190857.png)    
* Adaptive BN
    * 通过将均值方差修改为实际Domain的均值和方差，就可以完成Domain Adaptation


## [SyncBN](https://tramac.github.io/2019/04/08/SyncBN/)

> 解决了高显存占用模型(输入分辨率高)无法拥有大Batchsize的问题

* Plain BN with DataParallel - Input distiributed to subsets and build different models on different GPU    
    * 默认的形式是先将数据分组，然后在各个卡上独立完成前向与后向
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200418113212.png)
* (Since independent for each GPU, so batch size actually smallen)
* Key2SyncBN: Get the global mean & var
    * Sync 1st then calc the mean & var
    * (Simplest implemention is first sync together calc mean, then send it back to calc var) - Sync twice
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200418111031.png)
    * modify computation flow and only sync once        
    * 每张卡先做好Sum(x/x^2)传到其他卡，收集到所有的卡的信息之后做一个加和再进行运算
        * 直观想通信压力会大很多，吞吐量和同步都会存在
* [Code](https://github.com/tamakoji/pytorch-syncbn)
* [Explanation of Back-prop](https://kevinzakka.github.io/2016/09/14/batch_normalization/)