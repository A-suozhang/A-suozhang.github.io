---
layout:     post                    # 使用的布局（不需要改）
title:      迁移学习 Transfer Learning            # 标题 
subtitle:   能不能举一反三呢？        #副标题
date:       2019-10-18              # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-3.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 综述
---


# Transfer Learning 迁移学习

> 这个Field还是比较大，而且和Multitask啊Meta-Learning之间都有很多的勾连，所以系统入门比较困难，更多的还是一个感性的认知


* 和数据挖掘等领域联系也很紧密
* 会很玄幻...AAAI2017还有Distant Transfer Learning拿人脸来识别飞机 ...s
* ⭐有一个非常全的王晋东的[git材料集合](https://github.com/jindongwang/transferlearning/)，对于Survey来说做的已经很好了，可以直接拜读


## What 2 Solve 能做啥
* 解决大数据量的产生但是标注少的问题
* 普适性和专门化的矛盾

## Target 目标
* 本身并不是面向NN Training的，曾经是一个比较数学的ML的问题
* 将Well-Trained的模型的知识以某种方式分享给新的模型，从而避免**Training From Scratch**
* 一个核心问题是**找到新问题和原来问题之间的相关性**

## Taxonomy 分类
* 比较权威的杨强教授的[A Survey On Transfer Learning(2010)]()
* 几种常见的操作
    * 标注的数据不足的时候 -- 选类似的有标注的数据
    * 计算资源不足的时候/个性化任务 -- 把训练好的大模型迁移到我们的task中
* 几种常见的基底
    * 基于样本(Instance-based)
        * 直接对更接近的样本赋予更高的权限 
    * 基于特征(Feature-based)
        * 寻找一个新的特征空间，将两者的特征映射到同一空间处理(*目前比较有玩头的*)
    * 基于模型(Model-based)
        * 参数共享，比如finetune
    * 基于关系(Relation-based)
        * 挖掘类比-最玄学
* Inductive(归纳) TF （个人感觉这一类可能不是狭义上我们所认为的TF，下面的才是）
    * MultiTask --**Source/Target Domain Label Both Available**
        * 数据Label全都有，Task效果全都要
        * 最Naive的做法是前面的层共享，在最后直接加fc去fit不同的任务
        * Example
            * [Cross-stitch Networks for Multi-task Learning](https://arxiv.org/abs/1604.03539)
                * 用矩阵的Linear Combination融合特征(没细读，没理解和Naive的方法有多少区别  )
            * [Multi-task Self-Supervised Visual Learning](https://link.zhihu.com/?target=http%3A//openaccess.thecvf.com/content_ICCV_2017/papers/Doersch_Multi-Task_Self-Supervised_Visual_ICCV_2017_paper.pdf)
        * Question
            1. 网络架构的设计 - *去尽可能的利用转化信息（特征）*
            2. 分配训练权重： 由于数据集的体量可能有区别，类型可能也有区别，训练很有可能*被某个Task所Dominant*
        * [Survey](http://ruder.io/multi-task/)
    * Self-Taught -- **Source Unavailable; Target Available**
        * 比如用ImageNet提取到的特征用在其他Image Classification的
        * *Concerning Catastrophic Forgetting* 作者观点说*针对单一数据训练出来的模型，到另外一个Task基本都会迅速忘记,导致跨任务的泛化能力十分差*
            * 常见的解决是Freeze一部分，或者是加新的分支(和之前IL的调研吻合了)
* Transductive(转移) --**Source Domain Available;Target Unavailable**感性的来看比Inductive要难
    * Domain Adaptation
        * 目标是要学习出 Domain-Invariant的Feature
        * *将不同源领域的数据映射到同一个特征空间*
        * 不要求训练以及测试数据同分布
        * 要防止学习Overfittig于Source Domain （~~显而易见的问题~~）    
        * 现在的常规操作基本都不得不利用到Target Domain的一些信息，去尝试缩小covariance shift,还不能够完全利用Source Domain（那就通用人工智能了吧...）
    * [Unsupervised Domain Adaptation by Backpropagation](https://arxiv.org/abs/1409.7495)
    * [Learning Transferable Features with Deep Adaptation Networks](https://link.zhihu.com/?target=http%3A//proceedings.mlr.press/v37/long15.pdf)
    * Sample Selection Bias
    * Covariance Shift


## Examples 

### Deep Transfer Learning

#### 1. [CORAL-Correlation Alighment For Domain Adaptation](https://ssarcandy.tw/2017/10/31/deep-coral/)
* ~~很古老的一篇文章了，实现的应用场景很暴力， ECCV 2016~~
* 通过对Source与Task Domain的数据做一个线性变换，对齐它们的二阶统计量
* 实验：作者做了一个Unsupervised Learning的实验
    * Image Classification
    * *Office31 dataset* - 迁移学习的一个经典场景
        * 包含了3个数据集(Amazon DSLR Webcams)，3个数据集都有相同的31种类别,用来做Domain Adaptation
        * 使用了ImageNet Pretrain Model
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016165214.png)
    ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016165337.png)
        * 加入之后在Source上没有多少减小，但是task上多了很多
        * 两个Loss可以获得一个平衡

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016164502.png) 
    * 实际训练当中每个Batch包含了Source和Task数据(其中Task Data是没有Label的)两者通过同一个NN之后获得两个OUTPUT
        * 计算正常Loss以及CORAL Losss(由两者的softmax输出表示)
            * Coral Loss 在计算S&T Domain之间的协方差矩阵的差距

---

* 对于我们的Scenario，这种方式的问题是，实际来了我们没有见过的东西，会不会对模型有危害。

---

### 1.1 [Wasserstein Distance Guided Representation Learning for Domain Adaptation](https://arxiv.org/abs/1707.01217)
* 思路是降低Source和Task Domain之间的Wasserstein距离

### 1.2 [Semi-supervised representation learning via dual autoencoders for domain adaptation](https://arxiv.org/pdf/1908.01342.pdf)
* ~~2019年的一篇非常炫技的文章~~
* 目前的大多数结构都把Source Data和Target Data放到一起来从分布上减少他们的区别，同时获得一个Global Feature representation。
    * 但是没有考虑不同Domain中同一类别的数据差异这一信息
* 拿两个AE去同时学习Global以及Local的Feature
* 实验部分介绍了几个数据集
    1. Caltech Office - CORAL里面的
        * 被Extend而且修改过了，变成了4 Data Domain以及10类别
    * 别的几个都是文本的数据集，我输了


### 2. [DEN - Deep Adaptation Networks]()
* Michael J Jordan UCB & Tsinghua School Of Software

### 3. [Domain Separation Networks](https://arxiv.org/abs/1608.06019)
* [code](https://github.com/fungtion/DSN)
*  ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016173347.png)
* 很直观，利用两个Encode去争取获得两个Domain数据的Global Representation
    * 还有一个Decoder来提取重建误差
* ~~思路很明确，结构不是特别精巧~~
* 最后的实验是在Mnist上面做的，尝试从纯mnist，到random背景的mnist

### 4.[CSSA-Unified Deep Supervised Domain Adaptation and Generalization](https://arxiv.org/abs/1709.10190)
* [Code](https://github.com/samotiian/CCSA)
* ICCV 2017
* 吹的效果很好
* 实验很多

TODO: 回头精读一下

### [Adaptive Batch Norm](https://arxiv.org/abs/1603.04779)
* Peking Univ; TuSimple; SenseTime
* Cite Surprising Low(Probably Beacuse There're no Open Source Code)
* 解决的问题是Finetune的时候也需要大量带Label的图片
* 目标:增强模型的泛化性能
    * 不需要额外的参数与其他的部分
    * 证明可以Complementary其他工作


> We hypothesize that the label related knowledge is stored in the weight matrix of each layer, whereas domain related knowledgeis represented by the statistics of the Batch Normalization (BN)


* 这个假设的真实性存疑?但是实验上是Work的
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191017185803.png)
* 本质上是通过让BatchNorm随着实际的数据改变而改变，从而解决*Covariance Shift*的问题
    * 而默认情况是训练好了的网络里面的均值方差就是取的整个训练数据的
    * 文中
        > its core idea is to align the distribution of training data
        * 客观上也比较合理，因为原本的模型是针对Training Data的，自然要满足Training Data的分布
* Review BN
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191017191736.png)
    * 有两个可以学习的参数，x针对每个Mini-Batch把数据normalize
        * 保持每层的input distribution不变
        * 在测试阶段，Global Statistic Of All Training Sample去把每个Mini-Batch Norm了
* 做了一个实验证明了深层千层的Feature都可能受Domain Variance Shift
    * 不能只在最后一层做迁移（学到的Feature同样会受到Domain影响）
    * BN操作里面包含了Data Domain的相关信息
        * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191018090209.png) 
* 实际Scheme
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191017194353.png)
* 实际场景预估均值方差*有高效的办法[1999]*
* 后续思考？
    * 为什么一些简单的Rescale能够描述一个高度非线性的Domain Transfer
    * 为何独立的调整这些neuron的值，而不是像Coral一样做Decorelation
        * 作者认为由于Batch Size比较小所以Covariance Matrix不能完全反映
        * 另外非常耗费计算资源
* 实验结果
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191018090443.png)
    * 还尝试了更大规模的Caltech-Bing数据集
    * 当时主要对比的是CORAL这种方法，但是两者也可以结合起来
        * 很意外的是CORAL的end2end Deep DNN办法Deep CORAL效果始终不如Coral好
    * 由于需要截取Test Image的分布，做了一个*需要多少Test Image的问题*
        * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191018090845.png)


### [Deep Visual Domain Adaptation: A Survey](https://arxiv.org/abs/1802.03601.pdf)

* 2018(~~后面出来的一些模型基本上也不太好实现~~) 北邮
* 面对的场景:好的标注数据仍然不是很够
* 存在问题：Distribution change (Domain Shift)-会显著降低NN的性能
    * DA(Domain Adaptation) From *Source* To *Target*
* 分类
    * 粗分为两种
        1. Instance-Based
            * Reweight The Target Samples,and Train On It
        2. Feature-Based
            * 学习一个Shared Space,两个Dataset在该空间上是匹配的
                * 这里怎么个匹配法就是玩法了（很多都基于一些数学的性质比如Wasserstein距离，以及协方差（CORAL））
    * 经典Survey分为3种
        1. Inductive
        2. Transductive
        3. Unsupervised
    * 由于年代相对早，均考虑的是同质(homogenous)特征空间
        * 后续还有人研究heterogenous TL 
        * 两者的区别是，同质的只考虑了Distribution Shift，而异质的考虑当Feature Space也不一样的时候
        * 所以同质的时候Feature Space相同且with The Same Dimension(?)
        * 这其中，根据Data Accessible 的程度不同，分为Supervised,Semi-Supervised,以及Unsupervised
    * 我们一般默认Source和Target Domain是直接Related的，这样只需要一个
        * One-Step DA
        * Multi-Step DA

    * 这些年逐渐从ML的方法（Shallow TL）比如SVM等，相对数理的方法，到全面开始炼丹(Deep TL)
* Methods
    * 广义的Deep DA
        * 真正做的Knowledge Transfer的其实是Shallow Method（还是ML的那一套东西），NN其实只是做一个Feature Extraction或者说是做Representative Learning
    * 狭义的Deep DA
        * 都靠NN来干，embed DA Into The Learning Of The Representation
        * （端到端，更加适合我们的实际操作方式）
        * *3 Cases* 
            * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016205545.png)
            * 1. Discrepancy（矛盾）-Based：
                * 假设通过Finetune能够缓缓的消除两个Domain之间的Shift
                * 有4种操作方式
                    * Class-Criterion: Use Class Label Information As Guide
                        * *需要在Taeget Domain中有少量的数据Label*主要是利用Pretrain模型对这些Label的结果，来修正模型来达到Transfer的效果
                        * 当有足够多的Label的时候*Soft-Label*&*Metric Learning*
                        * 当没有的时候，借助Pseudo Label
                    * Statistics-Criterion:从数据的角度对齐两域的分布
                        * *MMD*&*CORAL*&*KL Divergence*
                    * Architecture-Criterion：通过修改网络的结构来学习到更Transferable的Feature
                        * *Adaptive BatchNorm*&*Domain Guided Dropout*
                    * Geometric-Based：更偏向从数据本身入手？
            * 2. Adversal-Based（对抗）
                * 构建一个Discriminator去判断数据是从Target还是Source Domain中来的，通过尝试去骗过这个Discrimnator，来Minimize两个Domain之间的区别(有一点抽象了哈)
            * 3. Reconstruction-Based
                * 通过重建数据来保证DA的有效性（这个假设真的成立吗？？？）
                * 这样可以保证确实获得了Intra-domain的Representation
                * 大部分基于*Encoder-Decoder*或者是*GAN*
            * *近几年的文章基本是靠后的，但是我们现在的情况还是更倾向于第一种的前两种Sub(相对比较Feasible一些)*
    * Discrepancy-Based中的比较可行的前两种
        * Class-Criterion:
            * *还是需要一些Task Domain的Label的*
            * 基本操作 (有一些是有点类似是一些Finetune的技巧了)
                1. 把新Label的GT与Pre-train Model的Softmax做为Loss训一训
                    * 有的时候用到了Hinton的Soft Label（也就是KD中的Softmax with Temperature）
                2. Metric Learning
                    * Semantic Alignment Loss
                    * Separation Loss
                    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016213039.png)
                3. Attribute Classification 
                    * 从High Level semantic attribute入手
                    * (?) Didn't Throughly Understood (On Page 6)
                    * [29] 基于Attribute做了Fine-Grain Recognition
                4. Pseudo Label
                    * 以最大后验概率的原则估计出假Label
            * 思路（很明确）
                * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016212718.png)
            * 感觉上和我们最需要的还是不是特别一致
        * Statistic-Criterion
            * 一般从分布的角度入手，专注与*学习到与Domain无关的Representation*
            * MMD是一个很关键的Metric（具体定义还挺数学的，不过应该是挺普适的一个东西）有着十分深厚的数理基础，第7页还有对这个东西的数学解释，没看
                * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016214234.png)
                * 本质上是通过一个Kernel function将原本Data映射到一个RKHS(Reproduced Kernel Hilbert Space)
                    * *DAN*，*JAN*,*RTN(Residue Transfer Networks)*
            * ⭐COROL则是学习一个（非）线性变换，将第二个Domain转化到第一个Domain
                * (相对来说最Feasible的一个了)
        * Architecture
            * Related Weight很直接很暴力
                * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016215626.png)
            * Adaptive BN:
                * [69] ⌛⭐认为Weight包含了区分Class的信息，而BacthNorm的信息包含了domain相关的信息，针对Target Domain做Adpative的BN，多加了一些Alignmnet params（BN中同时包含了原有的分布和新Domain的分布）
                * 后续人们发现当做了Instance Normalization之后，性能可以进一步提升
            * Domain Dropout
* 几种训练的情况
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016212028.png)
* 巨大的Performance表格
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191016220901.png)
* [一些比较有用的开源代码实现](https://github.com/jindongwang/transferlearning/tree/1938dff2a5085f1fb0c59fdb86d65045520ae72a/code/deep)
