---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读《Compressed CNN Training with FPGA-based Accelerator》          # 标题 
subtitle:   FPGA Training...   #副标题
date:       2019-11-15            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-nmb5.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
---

# [Compressed CNN Training With FPGA-Based Accelerator]

* 主要贡献
  1. training的gradient的量化需要高精度
     * 对WA采用量化,并对G进行累计
  2. Prune在一开始可能会伤及网络最后的Performance
     * 取了一个Half-Way,个人认为其实不是很Elegant
  3. 针对Sparsity的优化在各个计算部分操作机理不同
     * 提出了对几个Stage都实用的
  4. Configurable的Loop Mapping
  5. 做了一个Prototype System
* 几个Challenge
  * 之前的工作专注于模型的Finetune,这时候模型已经Converge了
    * 由于训练需要小步长的Learnning,与量化天然不符合
  * Sparse NN Training的优化
    * 目前的一些工作提出了Sparse Kernel,去跳0,我们叫他*Operator级别的Sparse*
    * 反传的时候会要求去对一个稀疏矩阵做转置
    * 所以我们设计了一个能支持这些Pattern的新的Sparsity处理单元


## Train Flow

* NN Train
  * FP (Forward Propogation)
  * BP (Back Prop)
    * EB (Error BackProp)
    * WG (Weight Gradient)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191115143042.png)
* Computing Pattern
  * EB阶段 
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191115143257.png)
    * 相当于对Output Error做了一个Rotated的Kernel的Conv
    * fc层就更简单

## Related Work

### Quantize

* 有些人用很小的WA,但是很大的G
* 有些人用CodeBook这种形式,能把位宽压下来
  * 但是实际参与计算的还是大位宽的数字,已经做一些Gradient的Statistic的统计的时候,也会有这样的情况

### Prune

* Channel Prunning其实属于3-d的Prunning了
* 目前Prune的work基本都是在训练完全之后再去做的

### NN INference Accelerator

* 一个好的NN加速器通常会考虑 Loop Unrolling , Loop tiling, Loop interchange
* 一个好的Unrolling,能够决定并行度的同时,也可减少带宽的限制
* *其实对于BP来说也存在着Nested的循环,所以我们也可以用一样的Pattern*

### Sparsity Accle

> 这个Sparse的思想其实和我的所谓Mask还有点一致性,其实是4-d的Sparsity??

* 很多人去加强Sparse加速的设计
* 去利用relu所带来的Sparse
* 现有的基本上基于operator,但是实际上在BP中,还会出现Result Sparse的情况
  * 被Prune掉的东西其实我们是不用计算它的

### Training Accel

* 一开始的一些加速器的设计还是用的fp32做G的运算,很菜
* FPGDeep引入了一种方法去将训练Map到大量的GPU当中
  * 用的是16比特的定点数,而且对各个Stage是分开讨论的


## Implemention

### Advanced Prunning

> 个人感觉不是很Elegant?

* 与别的Work不同,在整个网络Converge之前就做了一个Prunning
* 还是简单给予L2范数去做Prune

###　Quantize
* 使用的还是经典的Q方式,Dynamic Indice
* 选择了最近的2^N个去统计它的Distribution
  * 这样做Norm的时候其实可以用Shift
* 对BN来说,BN不需要一个准确的数值   
  * 所以可以等Float Training完了之后获得了大致准确的Distribution之后用量化过后的东西做  

## Hardware Design

* Quantize
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191115182652.png)
  * 每个样本存两份，一份是8比特的，一份是32比特的（都是定点）
    * 取出来计算的时候8比特
    * 存起来的时候32比特，参数更新的时候更新这个
  * 先进行一些Float Training让参数稳定在一个固定的区间
  * 位宽如果过小的话容易suffer weight Vanishing和不支持小的lr
* Prunning还是基于最基础的
* hardware Structure Overall
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191115185428.png)
* 随机读写对DDR不友好，所以设计了一个地址单元来（AGU）来缓冲
* 因为BP必须在FP之后执行，所以会牺牲并行度
  * 并行地算各个Stage，用一个PE Array
  * Layer BY Layer地算
  * 旁边搭一个Stream Processor来算Relu和Pool
* Controller与往常一样
* 基于稀疏操作做的Sparse PE
  * MP和EB是正常的Sparse
  * WG是两个dense的出一个Sparse
  * Sparse Matrix的Transpose
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191115193340.png)
* Loop Unrolling
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191115193445.png)
  * 前向和后向的卷积核对应的大小差异大，所以对Loop Unrolling提出了高要求
