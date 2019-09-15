---
layout:     post                    # 使用的布局（不需要改）
title:      A Survey Of Knowledge Distillation              # 标题 
subtitle:   The Most Advanced & Elegant Compression        #副标题
date:       2019-09-14              # 时间
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
* **后续的很多研究说明，或许KD并不需要一个很好的TCH**
  * 🤔是否只是一个比较好的Soft Label起到了效果而并非是KD
  * 其他研究中的Dataset Distillation验证了该观点 
* KD可以广泛使用与比如RL，在
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190913111544.png)
* 前期大家方法的主要区别是**What To Distill**
  * Origianl-KD: OUTPUT with Soft Target
  * ThinNet:     Intermediate Layer Weight
  * A Gift:      Flow Between Layer
* **Why Soft Target Work**
  * 让学生学到东西,不能只给一个答案,需要给一个完整的解答过程
* 过少的数据不能很好表达Teacher的所有Knowledge,所以需要一些额外的Data Augmentation
* **What To Worry**
  * 目前的一些Knowledge Distillation的方式有些看起来还是比较像在炼丹...那个压缩BERT的文章看上去很Appealing,但是只是比未蒸馏的高了5个点,还远达不到能够替代或者是上线
    * 目前水文章的一些人还是去关注怎么样通过一些trick去证明“能学出来”，去提升一些点
  * 不知道是蒸馏起了作用还是单纯的数据量增多了
    * 单纯的对比小模型直接训练，很容易就能超过
  * 概念很简单,但是实际实践起来有很多Trick层面的东西
  * （个人感觉）这种方式本质上还是针对OverParam，对于一个设计得比较elegant的网络架构压缩效率不高（类似于Deep Compression对VGG和ResNet）
    * 现有的论文尝试去Distill BERT效果只是稍有提升，和原模型差距还是很远...（感觉那篇文章与其说是去压缩BERT不如说是利用BERT的知识（但是作者好像本意就是后者，只是标题的意思有点像前者））
    * **Not Too Bad**理论上不会出现“对于一个distill得比较好的网络就不能用剪枝定点去压缩”这样子的问题
    * 但是我们也不是只用来做压缩，可能还要考虑这个东西对Transfer的应用（感觉肯定有人拿distillation做transfer这样的）
* **Where Did This Field GO**
  * 该领域原本提出是为了从大模型之中提炼出一个比较小的模型(如其名字所述)
  * 但是后续方向有些变化
    * 单纯用来更好地提升单个模型的效果,甚至有一些抛弃了TCH,只是互相学习(Deep Mutual Learning)
      * 有一些炼丹的意味
      * 有一定的应用价值
    * 应用于Transfer Learning
      * 感觉不是很多...(待寻)
      * A Gift 这篇文章提到过,但是不是特别合适
    * 深入地解释蒸馏的含义,更多trick,更多水文章...
      * Distill BERT
      * 感觉有些被引申为怎么提取出大模型中有价值的部分而更加深入研究DL的本质了,有点飘...

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

$$ G_{i,j}(s;W)=\sum_{s=1}^{h}{\sum_{t=1}^{w}{\frac{F_{s,t,i}^1(x;W)xF^2_{s,t,j}(x;W)}{h \times W}}}  $$

  * F1,F2是两个WxHxm的Feature Map
  * 选择TCH和STU分别生成的N个FSP Matrix做L2范数加入Loss（两者的Fsp Matrix大小需要相同（文章中对于不一样分辨率的使用了Max-pooling
    * ~~Not That Elegant~~
    * 两个网络分辨率需要相同感觉就像是resemble，而不太是distill（个人观点）
      * 作者说没有多少restrict，但是这个对STU的结构感觉限制有点多了
  ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910220809.png)
    * 实际训练时**2 Stage**学习FSP矩阵和学习任务是分开的 ~~Not So Elegant~~
  * 实验
    * 都使用ResNet on Cifar10/Cifar100
    * 锤了FitNet
    * 实验中还尝试了直接把weight复制过去，这确实是一种极端情况...
    * 实验中还证明了TCH的FeatureChannel是可以shuffle的，也就是FSP是可以shuffled的
    * Transfer Learning
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910223127.png)

### 4. [Net2Net: Accelerating Learning via Knowledge Transfer](https://arxiv.org/abs/1511.05641)
  * Tianqi Chen & Ian Goodfellow
  * ICLR 2018
  * 强调的是**Knowledge Transfer**和Distillation不完全类似,有一定联系
  * 更像是描述一种**Design Flow**，专注解决的问题是**Training Every Net From Scratch**是浪费，解决的是**Accelerate Training**
    * 与一般的Design Flow的最大区别是，**利用**预先训练的结构与模型而不丢弃它 （有那么一点Incremental的意味，作者在文中提到这种方法可以Smoothly instantiate a larger model）
  * 方法基于function preserving transformation(有点数学)目标是initialize STU 2 Represent The Same Function As Teacher
  * **Implemention**
    1. STU加入一些"Teacher Prediction Layer"，并修改损失函数，鼓励额外层去接近TCH网络中的某一层
      * 作者认为原理是： ```Teacher could provide a good interna Representation for the task```
      * 这种方法类似FitNets
      * 作者表示实验说明这种方法并没有比传统方法好太多  

### 5.[Deep Mutual Learning](https://arxiv.org/abs/1706.00384)
  * CVPR 2018
  * 单TCH单STU -> 单TCH多STU(STU之间还有Mutual Learning) -> 不需要TCH
  * 不仅可以从大模型中得到一个小模型，单纯的对大模型进行MutualLearning的效果也比单纯训练好
  > 感觉这就是换了一种形式的Ensemble(最大的区别就是实际上线的时候不需要多个Netorwk都跑取平均)
  ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190913101825.png)
  * Loss函数是正常的分类Loss加上该网络与其他网络的后验概率的KL散度
    * 对单网络来说相当于是加上了一个多余的惩罚项？（惩罚各个网络训练出来的差距过大？）
    * 多个STU的时候可以把其他K-1个STU的Ensemble当成一个TCH
  * 🤔这个东西的提出是不是说明了之前的大模型不必要？不需要一个非常接近效果好的模型（大模型），只需要一个比较接近效果好的模型（？），说不清楚
  * 实验
    * 数据集
      * Cifar100
      * Market 1501 （Person ReID - Fine-Grained）
    * 模型
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190913103015.png)
      * 小的是STU 大的是TCH （Inception V1； Wide ResNet）
    * Results
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190913103633.png)
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190913103702.png)
      * 与Distilation对比：
        * Peer As Teacher Is Better！
        * More Peers：Even Better Than More Network Ensemble    
    * Why Does This Work?
      * 不去寻找一个很陡峭很Deep的Train Set最优，而是去寻找一块相对Robust的最小值

### 6. [Born Again Neural Network](https://arxiv.org/abs/1805.04770)
  * Cite 76
  * ICML 2018
  * The BAN could OUTPERFORM the teacher in Image & Language Field
  * TCH与STU有完全相同的结构
  * (**Main Contribution**)认为KD的内容可以分为两个Term
    * 一个蕴含着错误信息的Dark Knowledge  DKPP
    * 一个GT相关的，只相当于原先gradient的简单rescale（?）  CWTM
  * Loss还是TCH和STU的Soft Target的Cross Entropy
    * 认为KD不需要一个Strong Master (with实验)
  * 实验(比较健全，之后具体分析)
    * 数据集： cifar100

### 7. [Using Knowledge Distillation Techniques To Improve Low-Precision Network Accuracy](https://arxiv.org/abs/1711.05852)
  * Low Precison + KD (强调KD可以显著提高low-bit网路的准确度)
  * **先用pretrain的Full-Precision，做量化之后，再用KD做Finetune，这样效果最好**
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190913145408.png)

### 8. [Feature Fusion for Online Mutual Knowledge Distillation](https://arxiv.org/abs/1904.09058)、
  * **从不同的Sub-Network中汇集Feature Map，本质上还是一种Ensemble的方式** 
  * 比较appealing的是提出了不同的subnetwork可以用不同的type（因为提取的是feature map） **But Not Different Task**
  * 实验中对比较和

### 9. [Knowledge Distillation by On-the-Fly Native Ensemble](https://www.semanticscholar.org/paper/Knowledge-Distillation-by-On-the-Fly-Native-Lan-Zhu/c864e3785a9aecf25296781c272980eaed78e51a )
  * **脱离Pretrained Teacher**
  * 单纯的通过KD,来提升训练某个模型的效果(一个该领域目前的主要应用点🤔感觉还是有一点偏炼丹的感觉)

### 10. [EnsembleNet: End-to-End Optimization of Multi-headed Models](https://arxiv.org/abs/1905.09979)


# Refs
> 以下文章按照实践顺序排列,且不可避免的会有一些比较没用的东西
1. [《Distilling the Knowledge in a Neural Network》阅读笔记](https://zhuanlan.zhihu.com/p/51550142)
2. [知识蒸馏knowledge distillation 在自然语言处理NLP中有哪些方面的应用或发展？](https://www.zhihu.com/question/333196499)
3. [Git-Awesome-Knowledge-Distillation](https://github.com/dkozlov/awesome-knowledge-distillation)
4. [数据集蒸馏](https://zhuanlan.zhihu.com/p/56328042)
5. [“在线蒸馏”训练大规模神经网络](https://zhuanlan.zhihu.com/p/35698635) Hinton,Google Brain; 处理分布式训练问题 
6. [ICML 2018 再生神经网络：利用知识蒸馏收敛到更优的模型](https://zhuanlan.zhihu.com/p/37384778)
