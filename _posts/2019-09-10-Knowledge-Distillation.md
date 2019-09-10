---
layout:     post                    # 使用的布局（不需要改）
title:      A Survey Of Knowledge Distillation              # 标题 
subtitle:   The Most Advanced & Elegant Compression        #副标题
date:       2019-09-10              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-term.jpg  #这篇文章标题背景图片
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
    * 道理上讲 **这样的Target具有更大的熵!**
* Use **Softmax With Tempeature**
$$ q_i = \frac{e^{\frac{z_i}{T}}}{\sum_j{e^{\frac{z_j}{T}}}}$$
  * Distillation Loss做了什么?
    * 由于正常NN输出的结果一般某一类的置信度很高,学不太出来东西,用温度因子T(*一般在1~20*)将各种类别的分布平缓一下(不然的话就是类似一个冲激,~~有点展宽胖胖瓣的意思~~)
* 目标函数:
  1. 只用Soft Target,蒸馏的时候TCH使用Softmax with T产生Soft Target,STU使用新的softmax在transfer set(迁移用的数据set,区分于Training set),STU与TCH使用相同的T
  2. 同时使用soft&hard,STU的损失函数是Hard与Soft Target的加权和
     * Hinton给了一个Dark Magic: 让Hard Target的权重小(因为在求导数的时候新的目标函数的梯度相当于都乘一个1/T^2,soft target对梯度的影响天然比Hard小) 
  3. 不用soft ~~废话~~ 
* Distillation与其他的Model Compression的方法(比如Pruning,Quantize)是可以结合的
* 试验证明Soft Target是可以起到正则化作用的
* **Why Soft Target Work**
  * 让学生学到东西,不能只给一个答案,需要给一个完整的解答过程
* 过少的数据不能很好表达Teacher的所有Knowledge,所以需要一些额外的Data Augmentation
* **What To Worry**
  * 目前的一些Knowledge Distillation的方式有些看起来还是比较像在炼丹...那个压缩BERT的文章看上去很Appealing,但是只是比未蒸馏的高了5个点,还远达不到能够替代或者是上线
  * 不知道是蒸馏起了作用还是单纯的数据量增多了
  * 概念很简单,但是实际实践起来有很多Trick层面的东西

# Papers List
### 1. [FitNets](https://arxiv.org/abs/1412.6550.pdf)
  * Yohsua Bengio ICLR2015

### 2. [Knowledge Distillation](https://arxiv.org/abs/1503.02531)
  * Hinton 
  * 领域的开山

## Clips
* [“在线蒸馏”训练大规模神经网络](https://zhuanlan.zhihu.com/p/35698635) Hinton,Google Brain; 处理分布式训练问题 
* 

# Refs
> 以下文章按照实践顺序排列,且不可避免的会有一些比较没用的东西
1. [《Distilling the Knowledge in a Neural Network》阅读笔记](https://zhuanlan.zhihu.com/p/51550142)
2. [知识蒸馏knowledge distillation 在自然语言处理NLP中有哪些方面的应用或发展？](https://www.zhihu.com/question/333196499)
3. [Git-Awesome-Knowledge-Distillation](https://github.com/dkozlov/awesome-knowledge-distillation)
4. [数据集蒸馏](https://zhuanlan.zhihu.com/p/56328042)