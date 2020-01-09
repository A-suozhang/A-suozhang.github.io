---
layout:     post                    # 使用的布局（不需要改）
title:      Compression相关的一些内容...          # 标题 
subtitle:   将来吹牛逼可能会用到?...   #副标题
date:       2019-11-17           # 时间
author:     tianchen                      # 作者
header-img:  img/11_30/road0.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 综述
---


# Resources 

> [Neural Network Quantization Resources](https://jackwish.net/neural-network-quantization-resources.html)

> [NN 量化算法简介](https://jackwish.net/neural-network-quantization-introduction-chn.html)

> [Distiller Document- 算法实现方法](https://nervanasystems.github.io/distiller/quantization.html)



---

# [Neural Network Quantization Resources](https://jackwish.net/neural-network-quantization-resources.html)

* 工业界的主要操作
  * 很多学术界的操作需要引入额外的模块,实际操作起来困难
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191117104628.png)

* 数据的表示方式
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191117132527.png)

* 量化乘法
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191117133016.png)

* 最简单的量化
  * 均一量化
  * 由于NN的参数分布相对集中(很少会超出1)
  * 将浮点数的值域直接通过一个Scaling Factor映射到定点数的范围
    * 2^31个浮点数被映射到了256个值
    * 浮点越接近0,表示的会越精确,而定点没有考虑(其为恒定密度的)
  * 量化数的乘法可能会将位宽拓展到16

* 第一个最简单的改进就是选取**合适的区间**而不是整个分布区间,同时对超过该区间的参数做Clip
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191117134407.png)
  * 怎么寻找这个合适的区间(也就是一个[min,max]),被称为**值域调整方法**
    * 目前有训练时和训练后的量化办法
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191117134813.png)
      * *训练后*(TF)常规训练一个模型 - ```tf.contrib.quantize```,重写网络,通过插入```Fake Quants```节点来训练出这个区间,用```TF lite重写并Finetune```
        * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191117135036.png) 
        * Fake quant节点在训练的时候量化为了int8 (严格来说还是fp32,只不过计算过程中计算了Quan/DeQuan)),去训练这区间
        * 只有前向的时候用了int8,反向的时候没有用,还是fp32
        * 这种训练方式叫做quantize aware training(量化感知训练)
          * (稍有不准确因为实际的量化感知训练不仅包括了训练这个Range也包括了最直接的WA量化)
      * *训练时*将寻找区间和量化合并,叫做**Calibration**,在Nvidia TensorRT以及MXNet中都有
        * 相当于把原来的2 Stage变成一个1 Stage
* 目前飞哥的量化工具支持的方案就是
  * 在前向的时候，首先遍历这一层的所有weight，找到range，做均匀量化，之后再计算
  * 算出Activation之后的操作同理
  * Loss的Backward的传播是STE的形式，就是保留原本的Weight
  * 参数更新也同理

# [Development Of Quantize](https://jackwish.net/neural-network-quantization-resources.html)

* (2016) - LOW_BIT_NET
  * BNN
  * BinaryConnect 
  * QNN (Quantized NN)
  * Ternary Weight 
  * XNORNet

---

* (2016) - NET_COMPRESSION
  * Deep Compression 
  * Fixed Point Q For CNN (Qualcom) - SQNR Analysis
* (2017)
  * Apprentice : Use KD For Quantize
  * Flexpoints: Adaptive Numerical Format
    * *Still Floating,exponential is flexible*
  * TensorRT Calibration Based On KL Divergence
    * 对Q前后的Activeation分布求KL散度
  * Mixed Precision Training
    * Dynamic Fixed Point 
  * Two-Step Quantization for Low-bit Neural Network
    * 第一步学code,第二步学transform function


# [Distiller Doc](https://nervanasystems.github.io/distiller/quantization.html)

* 早期方法的很多Scaling Factor用的还是浮点(*为了解决分辨率的问题*)所以其实还是要浮点计算的
* log2(c⋅k2)每次卷积最后的Acccumulator的大小,一般取32比特
* Offline与Online的Q,主要区别就在于如何去选取Range
  * online的方法就不允许Clip了
* 不做Retrain的方式叫做Post-Training Q,对于比如Mobilenet这种小而高效的模型效果不好
  * 所以需要Quantize-Aware Training
* 对比较激进的量化 **4 and lower**
  * 一般都需要一个比较好的fp32模型作为起点或者是作为Teacher
    * Train From Scratch不大可能
  * 不用Relu
    * 因为relu是无界的,容易把整个分布拉坏
    * 类似的思想还有对每一层学习一个Clipping Value
  * 第一和最后一层更加需要高比特
  * Iterative Q
  * Mixed Precision

---

* 实际最简单的量化方法被分为了是否对称
  * 需要一个zero-point加上一个Scaling factor,相当于完成了浮点数的[min,max]到定点数的映射
  * 对称的方法则是直接将浮点数最大值的绝对值作为128,利用-128到128的范围来表示浮点数
    * 两者的零点是对齐的
    * 不能很好的处理Float分布biased的情况
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191117163235.png)

# [Analysis Of Quantized Network]()
* 分析了weight和gradient的quantize带来的影响
  * 量化weight的问题是为了分布式训练的时候传输weight快
  * 提出量化weight多少都会给最后的结果带来影响
  * Weight Clipping能够有效防止由于Range带来的问题


# [A Survey on Methods and Theories of Quantized Neural Networks](https://arxiv.org/pdf/1808.04752.pdf)

* Dec 2018 

### Background
* Use Bit-wise operation instead of floating operation
* Category
  * Deterministic: 一对一
  * Stochastic
* Codebook
  * Fixed
  * adptive: Codebook中的值是训练出来的
* Quantize网络的一个主要问题是很需要tunning

### Quantize Technique

#### Rounding

* 最简单的Binarize用了Sign函数，但是不可导
  * 用hardtan来近似
  * Hinton提出的STE(Staight-Through Estimation) - 用这样的一个函数(只有原来的x绝对值小于1的时候才计算梯度)，来替代本身应该的binary函数的导数
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123152610.png)
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123152829.png)
* 拓展到多bit的Rounding（把一个浮点数round到离他最近的fix-point representation）
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123153356.png)
  * 一般都会首先做一个Clip  
* 有的时候rounding的fix-point不是等距离的，是拿对数的
* 有一些缺点
  * 虽然实现起来很简单，但是性能下降明显
  * 一般都需要把全精度的值存下来，为了训练，所以memory overhead还是很高
  * 否则可能的param space比较小了，就更难收敛了
  * 同时并没有用到结构信息
  
#### Vector Quantize

* cluster参数成一个group并且用centroid来代表这一簇，然后用其代替
* 最早的就是用k-means
  * 单纯使用k-means不能控制精度损失
  * 也没有constraint rate的ratio
  * 改进了一个*Hessian-Weighted K-Meanss*
* 据说基本能在imagnet上达到16~25倍左右的compression rate
* 后续有人做过Product Quantize（更加细粒度(物理)的VectorQ）
  * 把一个matrix分成几个sub-matrix，并且对他们分别做Quantize
* 问题
  * k-means computation expensive
  * 只能用于Pretrained Model

#### View Of Optimization

* XNORNet中作者就有这样的思路
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123160503.png)
  * W是原本的weight mat，a是一个scale factor（是所有w中元素L2范数的mean），B对应着Binary的mat
  * *我理解是将其看成一个优化问题，注意到了整个层的weight分布，而不是像STE一样对每一个weight独立处理*
  * 后续的一些工作改动为了多个scale factors，因为考虑到了正负值之间的不对称性
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123161351.png)
* 另外一种流派是 Loss-Aware-Quantizing  
  * 抽象为数学优化问题
  * ADMM
* 缺点
  * 收敛性能不能很好的被保证

--- 

#### Random Rounding
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123162226.png)
  * 本质就是让一个fp去按照概率被归类到他接近的fixed点上
  * 相当于给训练过程加上了噪声，也可以认为是一个*Regularizer*
* 缺陷
  * Random Sample会使得Gradient的variance大，而导致训练曲线震荡

#### Probabilistic Quantize

* 受到整个weight的参数都比较小而且基本是正态的
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123163024.png)
  * 以这个为依据（中心极限定理）去加入算法调整




### Methods

* 都可以认为是基于CodeBook的，内部内容是否固定也可分为以下几种
  * * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123163638.png) 
  * *A small codebook means that we can only search the parameters in a limited space, which makes the optimization problem very hard. With predefined*
  * Codebook不固定的又分为Hard或者是Soft的
* 也可以分为Quantize While Training
  * BinaryConnect
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123171658.png)
  * XNORNet
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123171719.png)
  * DoReFa
    * 对XNOR的rounding加了一些操作，改进了
  * ABC
    * 分析了为什么binary可能会fail
      * 用更小的lr
      * 用了pRelu
* Quantize After Training
  * DeepCompression
  * EntropyConstraintScalarQuantization
    * 对loss做泰勒展开，舍弃高阶项，尝试去寻找那些weight重要
  * IncrementalQuantization
    * weight parttion - group-Q
    * 每一次一半的weight被量化，另外一半保留，去fintune，直到最后全部的都被量化

---

### Quantize Components

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123165729.png)

#### Quantize Weights

* Layer-Wise的weight Quantize可以提升性能
* 有的工作尝试去找到每个layer所需要的bit-width
* 有人用很多个binary base的加权去近似weight（？？？这是再干啥）
* 特点
  * weight-Q的网络更难收敛，一般都需要更小的lr
  * 由于准确的weight不可得到，需要找到low variance，small bias的weight
  * 怎么locally完成quantize也是一个问题

#### Quantize Activation

* 只有完成了activation的quantize才能让计算变成bitwise的
  * *只quantize weight是不行的，严格来说松哥的那篇并没有加速计算*
* 一开始quantize activation的时候是用了sigmoid当激活函数，因为这样可以将activation限制在有界范围内
* WRPN(Wide Reduced-precision Netowrk)发现了其实weight所占用的内存没有activation大，所以用很多的filter堆起来保证性能(不带劲)

* activationQ比weightQ难的原因
  * 需要backprop过一个不可微分的算子
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123165417.png)
    * 如果这里的gj是一个binary的operator，导数几乎就是0
  * 即使做了处理，也可能出现*Gradient Mismatch*
    * *the gradients flow through the discrete neurons*


#### Quantize Gradient

* 应用场景在Distributed Training需要很多的Sub-Gradient
* QSGD - Grad的random rounding
* 有人选择每次选择一定比例地去Q（和我们的应用场景不一致）

* 对于单机来说，甚至有人直接用2bit的Weight
* 有文章表示随机的rounding会更有效果
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123170702.png)
* 梯度量化特别难的原因是
  * 幅度值和符号都很重要
  * naive的方式不太能成立

### 从Codebook角度

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191123170838.png)


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