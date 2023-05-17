---
layout:     post                    # 使用的布局（不需要改）
title:      Things About Attention          # 标题 
subtitle:   Kochi o miru!            #副标题
date:       2019-09-30            # 时间
author:     tianchen                      # 作者
header-img:  img/bg-ucentre-rain.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 综述
---

# Attention
* 能被广泛应用于*机器翻译，语音识别，图像标注*
* **核心思想**-对输入各个部分授予不同权重，提取更加关键的信息，而且*不会对存储和计算带来更大的开销*
* **本质**就是另外添加了一组权重
    * 根据某些规则(这些规则从哪里来的可以有玩法)对学习出来的Vecotorized Representation Set抽取特定的进行**加权组合**的方式(怎么个加权组合法也可以有玩法)
        * 这个Vectorized Representation Set在Encoder-Decoder中是那个中间的表示，（在CNN中就是学出的Feature Map？）
* **玩法**
    1. 在加权求和的计算方式上创新
        * 叫做一个“Score“函数，可能包括*点乘，点积，concat*
    2. 在Attention Score(匹配度)的计算方式上创新
    3. 迁移性创新(两者结合，也就是在上面本质中的两个环节做修改)
* **起源**：
    - 在NLP中使用注意力机制，解决的是**Lose Focus的问题**
        * 一开始用于解决*输入尺寸过长的时候效果差的问题(Lose Focus)*，由于Encoder-Decoder的架构将输入X映射为一个固定长度的隐向量Z(其*固定长度*并且对于其中每一位都赋予*一样的权重*，这样不是很合理)
        * 在CV中同理，因为对图像的每个区域与尺度做了一样的处理，当图像尺寸大的时候，也容易*Lose FOcus*
    * Simple Seq2seq: ![](https://pic3.zhimg.com/80/v2-d0ac5d94a479971f134290c4b93d8cee_hd.jpg)
    * Seq2Seq with Attention
        * 主要就是中间那个隐节点，从sum变为加权sum
            * 这一个加权的值，不仅可以认为是包含了*各部分本身的重要程度*而且也可以反映*输入部分各时刻对输出部分某一时刻的重要程度(一定程度上反映了一个“对齐”)*
        * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190930102855.png)
    ---
    * 成名于<Attention Is All You Need>，其
        - 之前的RNN里面用到了大量的Encoder-Decoder结构(由于前后隐藏状态具有依赖性，所以不能并行，所以**慢**) 
        - 提出Transformer完全替代RNN&CNN
        * Transformer Architecture：![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190928224114.png)
        * Attention implemention ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190928224243.png)
        
## Taxnomy
* **Hard/Soft** 默认的Attention方式是参数化的(有梯度回传，参数在训练中被调节)，*会考虑到全部位置的信息*
    * Hard Attention相对而言更加硬盒*更加随机*，*只注重于当前位置的信息*,完全随机化,用monte-carlo来估计梯度
    * 还有**Local Attention** 半软半硬 
        * 用一个人工选择的参数确定范围
* **Global/Local**
    * Global-考虑encoder的所有的hidden state
    * Local(见上)

## Related Knowledge
* Bi-LSTM:hidden state j,描述了第j时刻的状态以及之前的一些信息(逆序输入则包含了第j个单词后的一些信息)*两者结合就同时获得了j前后的一些信息*



## Refs
* [知乎-Attention Mechanism详解](https://zhuanlan.zhihu.com/p/57501837)

