---
layout:     post                    # 使用的布局（不需要改）
title:      A Survey Of Knowledge Distillation              # 标题 
subtitle:   The Most Advanced & Elegant Compression        #副标题
date:       2019-09-10              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-term.png  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - DL
    - 综述
---

# Knowledge Distillation
> 这不出意外是一个大坑,将来会将很多资料整合到这篇博文之中,其中每个小部分可能独立为其他文章
> 希望能够好好坚持做完

# Overview & 🤔Thinking
> 在熟悉一个领域之前,首先有一些感性的认识,可以边读边思考,将来的一些思考内容也将汇入这里
* **Why Knowledge Distillation**
  * The Model Is Too BIG (~~屁话~~)
* **What Is Knowledge Distillation?**
  * Distill/Transfer Knowledge from (a set of)large cumbersome models to a light single model (without significant performance loss)
* **Trade Off** Bigger Model(If Properly Designed) Could **Perform Better** But **More Computation**
  * 极端情况: Ensemble - 几乎能保证提高performance,但是线性增加计算量
* Knowledge - Where did it from?
    1. The *Params W* in NN
        * 这个视角指向了*finetune*:
          * 先在大数据集(ImageNet)上训练,然后再在小数据集上微调(COCO),训练的是同一个网络 
    2. The Learned **Mapping** (More Abstract)
        * Knowledege Distillation 从这个视角切入
* **How Do Knowledge Distillation Do**
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910165840.png)
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910171736.png)
* Stu不是直接从Training Data中获取数据,而是学着去followTCH
  * 所以学习的是Soft Target: 以分类问题为例子,不是一个one-hot的vector而是各类别的softmax
  * 为什么这样好?
    * 直观的看,更好的还原的事实,如果teacher model train的不错的话,确实会有几类比较难区分的,这个比只有一类是对的更加接近真实
      * 可以认为是TCH学到了这几类比较相似,并且将这个信息传递给STU
    * 道理上讲 **这样的Target具有更大的熵!** 而且这样的类之间梯度的方差更小
* Use **Softmax With Tempeature**
$$ q_i = \frac{e^{\frac{z_i}{T}}}{\sum_j{e^{\frac{z_j}{T}}}}$$
  * Distillation Loss做了什么?
    * 由于正常NN输出的结果一般某一类的置信度很高,学不太出来东西,用温度因子T(*一般在1~20*)将各种类别的分布平缓一下(不然的话就是类似一个冲激,~~有点展宽旁瓣的意思~~)
* 目标函数:
  1. 只用Soft Target,蒸馏的时候TCH使用Softmax with T产生Soft Target,STU使用新的softmax在transfer set(迁移用的数据set,区分于Training set),STU与TCH使用相同的T
  2. 同时使用soft&hard,STU的损失函数是Hard与Soft Target的加权和
     * Hinton给了一个Dark Magic: 让Hard Target的权重小(因为在求导数的时候新的目标函数的梯度相当于都乘一个1/T^2,soft target对梯度的影响天然比Hard小) 
  3. 不用soft ~~废话~~ 
* Distillation与其他的Model Compression的方法(比如Pruning,Quantize)是可以结合的
* 试验证明Soft Target是可以起到正则化作用的
* 前期大家方法的主要区别是**What To Distill**
  * Origianl-KD: OUTPUT with Soft Target
  * ThinNet:     Intermediate Layer Weight
  * A Gift:      Flow Between Layer
* **Why Soft Target Work**
  * 让学生学到东西,不能只给一个答案,需要给一个完整的解答过程
* 过少的数据不能很好表达Teacher的所有Knowledge,所以需要一些额外的Data Augmentation
* **What To Worry**
  * 目前的一些Knowledge Distillation的方式有些看起来还是比较像在炼丹...那个压缩BERT的文章看上去很Appealing,但是只是比未蒸馏的高了5个点,还远达不到能够替代或者是上线
  * 不知道是蒸馏起了作用还是单纯的数据量增多了
  * 概念很简单,但是实际实践起来有很多Trick层面的东西
  * （个人感觉）这种方式本质上还是针对OverParam，对于一个设计得比较elegant的网络架构压缩效率不高（类似于Deep Compression对VGG和ResNet）
    * 现有的论文尝试去Distill BERT效果只是稍有提升，和原模型差距还是很远...（感觉那篇文章与其说是去压缩BERT不如说是利用BERT的知识（但是作者好像本意就是后者，只是标题的意思有点像前者））
    * **Not Too Bad**理论上不会出现“对于一个distill得比较好的网络就不能用剪枝定点去压缩”这样子的问题
    * 但是我们也不是只用来做压缩，可能还要考虑这个东西对Transfer的应用（感觉肯定有人拿distillation做transfer这样的）

# Papers List
### 1. [FitNets](https://arxiv.org/abs/1412.6550.pdf)
  * Yohsua Bengio ICLR2015
  * 在KD提出之后，更进一步，提出*不仅使用最后的结果*而且也使用*TCH的中间层的结果*（由于TCH一般比STU大，所以还需要引入额外的参数来做一个映射），如标题说，给了STU一个**hint**，让STU变得**Deeper&Thiner**
    * hint指TCH的某一层，STU需要去拟合这一层 （在Loss中加入一项是他们两层的L2NORM）
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910211632.png)

### 2. [Knowledge Distillation](https://arxiv.org/abs/1503.02531)
  * Hinton 
  * 领域的开山，主要贡献就是提出了 Softmax-With-Temperature
  * Experiment：
    * Mnist
      * 带Distill的小网络（从大网络中获得了信息）比直接训练小网络的效果显著提升，并且保持了泛化能力
      * 另外，把实验数据里面的所有“3”删除（对于小网络来说3在trainingset中不存在,只来自TCH）仍然可以取得较好的效果（而且其实精度损失很大程度上来自于训练数据imbalance所带来的bias）

### 3. [A Gift from Knowledge Distillation](http://openaccess.thecvf.com/content_cvpr_2017/papers/Yim_A_Gift_From_CVPR_2017_paper.pdf)
  * CVPR 2017
  * Another Perspecive：**The Flow Between Layers**
  * 效果
    1. 更快收敛
    2. 效果更好
    3. 具有一定迁移能力（different task）
  * 举了一个例子：输入是问题，输出是结果，中间过程就是中间步骤
  * Core:**The FLow Of The Solution** - Direction Between Features Of 2 Layers  
    * The **FSP Matrix**

### 4.[Deep Mutual Learning](https://arxiv.org/abs/1706.00384)
  * CVPR 2018

$$ G_{i,j}(s;W)=\sum_{s=1}^{h}{\sum_{t=1}^{w}{\frac{F_{s,t,i}^1(x;W)xF^2_{s,t,j}(x;W)}{h \times W}}}  $$
    * F1,F2是两个WxHxm的Feature Map
    * 选择TCH和STU分别生成的N个FSP Matrix做L2范数加入Loss（两者的Fsp Matrix大小需要相同（文章中对于不一样分辨率的使用了Max-pooling）~~Not That Elegant  ~~）
      * 两个网络分辨率需要相同感觉就像是resemble，而不太是distill（个人观点）
        * 作者说没有多少restrict，但是这个对STU的结构感觉限制有点多了
  ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910220809.png)
    * 实际训练时**2 Stage**学习FSP矩阵和学习任务是分开的 ~~ Not So Elegant ~~
  * 实验
    * 都使用ResNet on Cifar10/Cifar100
    * 锤了FitNet
    * 实验中还尝试了直接把weight复制过去，这确实是一种极端情况...
    * 实验中还证明了TCH的FeatureChannel是可以shuffle的，也就是FSP是可以shuffled的
    * Transfer Learning
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910223127.png)


### 4. [Born Again Neural Network](https://arxiv.org/abs/1805.04770)
### 5. [Using Knowledge Distillation Techniques To Improve Low-Precision Network Accuracy](https://arxiv.org/abs/1711.05852)
### 6. [Feature Fusion for Online Mutual Knowledge Distillation](https://arxiv.org/abs/1904.09058)
### 7. [Knowledge Distillation by On-the-Fly Native Ensemble](https://www.semanticscholar.org/paper/Knowledge-Distillation-by-On-the-Fly-Native-Lan-Zhu/c864e3785a9aecf25296781c272980eaed78e51a )
### 8. [EnsembleNet: End-to-End Optimization of Multi-headed Models](https://arxiv.org/abs/1905.09979)

## Clips
* [“在线蒸馏”训练大规模神经网络](https://zhuanlan.zhihu.com/p/35698635) Hinton,Google Brain; 处理分布式训练问题 
* 

# Refs
> 以下文章按照实践顺序排列,且不可避免的会有一些比较没用的东西
1. [《Distilling the Knowledge in a Neural Network》阅读笔记](https://zhuanlan.zhihu.com/p/51550142)
2. [知识蒸馏knowledge distillation 在自然语言处理NLP中有哪些方面的应用或发展？](https://www.zhihu.com/question/333196499)
3. [Git-Awesome-Knowledge-Distillation](https://github.com/dkozlov/awesome-knowledge-distillation)
4. [数据集蒸馏](https://zhuanlan.zhihu.com/p/56328042)