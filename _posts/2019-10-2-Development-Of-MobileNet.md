---
layout:     post                    # 使用的布局（不需要改）
title:      MobileNet的发展历程          # 标题 
subtitle:   轻量化网络思路概览        #副标题
date:       2019-10-2            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-jianxiong2.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - CNN
---
# MobileNet
* Google出品

## MobileNet V1
* [MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications](https://www.zhihu.com/search?type=content&q=mobilenet)
* 2017.4
* vgg16的结果Conv换成DSC
* 与Xception都用到了深度可分离卷积
* **核心**：**深度可分离卷积(Depthwise Seperable Convolution)**
    * *可分离卷积*(说白了就是怎么简化卷积的计算)有
        1. 空间可分离
            * 3x3 -> {1x3}x3
            * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191002095930.png)
        2. 深度可分离
            * **DepthWise**把卷积核拆分为单channel的形式，对每一个通道进行卷积，这样就不会改变输入图像的尺度
                * (这个步骤卷积核的个数要求是和输入图像的channel数一样，比如输入RGB，就对应3个卷积核)，*这样feature mao的Channel数目过于少了*，所以需要下一步的Pointeise Conv
            * **PointWise**本质就是1x1Conv，可以自由地对Feature Map进行升维和降维
            * 对比 
                * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191002101511.png)
            * 参数和计算量变成原先的1/N（对于3x3大概是8或者9分之一），而准确率几乎没下降
            * **What's Lacking** DSC比较容易训练崩，原因是很多值容易变0，估计是*Relu出了问题*
                * 对低维度做Relu很容易损失信息(可以理解为加起来的值比较小容易是负的，而DSC卷积核浅)
                * 将ReLU变为Leaky ReLU （*然而MobileNet V2把某曾Relu直接变成了线性*）
            * **Why Could This Work？**
                * 个人猜测是传统卷积的先在各个Channel做Conv然后逐channel叠加起来这一步损失了信息，而depthwise conv只用一层的卷积核，填补了这一部分
                * *MobileNet主要利用了1x1卷积*其特殊之处在于*省去了im2col的过程*
                * 这篇文章用一图表解释为何 [为什么MobileNet及其变体如此之快？ - 机器之心的文章 - 知乎](https://zhuanlan.zhihu.com/p/64138403)

## MobileNet V2
* [MobileNetV2: Inverted Residuals and Linear Bottlenecks](https://arxiv.org/abs/1704.04861)
* **主要改进**
    1. **Linear Bottleneck**把最后的一个Relu换成*线性激活*
    2. **Expansion** 由于DepthConv不会改变FeatureMap通道数，所以该卷积都存在于稍浅层，为了学习到好的特征，先经过一个PointWiseConv进行升维之后，再进行维度压缩(*之前是先卷再升维度，现在是先拓张再卷积再收缩*)
    3. **Inverted Residue**和ResBlock方法类似
        * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191002202729.png)
        * Invert在Res是先降维，再3x3卷积，再拓展；而MobileNet是先拓展

## MobileNet V3
* [Searching for MobileNetV3](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1905.02244.pdf)
* NAS，告辞...

## Refs
* [为什么mobilenet，shufflenet在理论上速度很快，工程上并没有特别大的提升？](https://www.zhihu.com/question/343343895/answer/816049923)
    * 更底层的思考
    * 计算强度

