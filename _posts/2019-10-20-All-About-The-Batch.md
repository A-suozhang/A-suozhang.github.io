---
layout:     post                    # 使用的布局（不需要改）
title:      与Batch相关的一些思路          # 标题 
subtitle:   All About The Batch   #副标题
date:       2019-10-20            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-ucentre1.jpg  #这篇文章标题背景图片  
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

