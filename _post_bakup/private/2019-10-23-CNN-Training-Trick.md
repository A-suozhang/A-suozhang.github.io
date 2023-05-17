---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读《Bag of Tricks for Image Classification with Convolutional Neural Networks》          # 标题 
subtitle:   Awesome Trainig Tricks   #副标题
date:       2019-10-23            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-dorm1.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
     - CNN
---

# 论文阅读[《Bag of Tricks for Image Classification with Convolutional Neural Networks》](http://openaccess.thecvf.com/content_CVPR_2019/papers/He_Bag_of_Tricks_for_Image_Classification_with_Convolutional_Neural_Networks_CVPR_2019_paper.pdf)

* CVPR2019

* 整理了一些CNN文章中的Refinement(从开源代码中提取出来)并且分析
* 给ImageNet上的Res50提了4个点(75-79)
* 说明了更好的分类效果(BaseBone)能够提升迁移学习的能力

---

### Basline Implemention(常规操作)
* 预处理与数据增强
  * 随机Crop并且转化为[0,255]之间的32位宽浮点数
  * 随机Crop的aspect ratio在3/4到4/3
  * 大小从8~100%
  * 50%概率水平翻转
  * 最后被Resize到[224,224]
  * 加上PCA Noise
  * 对饱和度,亮度等等取[0.6,1.4]之间的正态Sample
  * *最后Normalize整个图片*
* (对测试的时候,我们不做增强,首先对图片按照短边resize到256,再随机Crop到244,然后Normalize)
* 采用Xavier初始化
* 使用加Nesterov加速的SGD(NAG)
  * batch-size: 256
  * 共训练120 epoch
  * lr  0.1(30,60,90 epoch上x0.1)

### Efficient Training - 提升速度
* 用更大的Batch更快,但是会有问题
  * 凸优化问题的本质决定的,更大的Batch-Covergence Rate更小...
  * 也就是为什么会一开始用Mini-Batch!
  * 有一些研究尝试解决这个问题
    * 有人认为可以Linear-Scaling Lr with BatchSize  
      * 256对应着0.1
      * ~~按照我自己测试的结果大BatchSize这样一样会炸裂~~
    * LR-WarmUp
      * 一开始从0线性变大到初始lr
    * 让ResBlock中BN中wx+b的参数w,一开始为0(??)
    * 取消bias decay
      * weight decay相当于L2 Norm
      * 有些方法把weigh和bias同时给decay了
    * 还有对不同layer取不一样的Learning rate的
      * 能够适应很大的batch size
      * lzzscl...
* Low-Bit Training会更快 - 主要是用fp16
  * 其实主要还是GPU运算的速度快了,效率按照完全看来其实比不上fp32
  * 基本没有Accuracy的损失
* Network Tweak - 对结构进行微调
  * **ResNet结构分析**
    * 1个输入Stem,4个中间环节,一个output layer
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191023163927.png)
      * Input Stem: 7x7 Conv (Stride 2) + 3x3 MaxPool (Stride 2)
        * Width 64 InputDownSample x4
      * Stage (首先一个DownSample,接着几个ResBlock)
        * DownSample Block (双路)
          * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191023164434.png)
        * ResBlock
          * 个数不同,导致ResXXX


### Training Refinement - 提升精度
* Cosine Learning Rate Anealing
  * 比较原先的LR的Step Decay,选用了Cosine方式的Decay
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191023170729.png)
* Label Smoothing
  * 传统的直接拟合one-hot label,直接计算Softmax的方法
    * 可以被理解为:*Encourage一个非常Distinctive的模型*从而lead To Overfitting!
    * Solution: 咱把Onehot的Label给改了吧
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191023171246.png)
* KD
  * ~~我就知道有你~~
* MixUp Training
  * 也可认为是一种数据增强