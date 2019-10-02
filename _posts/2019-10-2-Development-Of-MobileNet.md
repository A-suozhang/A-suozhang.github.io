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
---
# MobileNet
* Google出品

## MobileNet V1
* [MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications](https://www.zhihu.com/search?type=content&q=mobilenet)
* 2017.4
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
            * **Why Could This Work？**
                * 个人猜测是传统卷积的先在各个Channel做Conv然后逐channel叠加起来这一步损失了信息，而depthwise conv只用一层的卷积核，填补了这一部分

