---
layout:     post                    # 使用的布局（不需要改）
title:      Compression相关的一些内容...          # 标题 
subtitle:   将来吹牛逼可能会用到?...   #副标题
date:       2019-11-17           # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/ucentre2.jpg  #这篇文章标题背景图片  
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
