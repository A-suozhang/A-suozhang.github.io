---
layout:     post                    # 使用的布局（不需要改）
title:      Paper Stack 文章堆栈           # 标题 
subtitle:   待读的文章和无处安放的文章解读   #副标题
date:       2020-03-26            # 时间
author:     tianchen                      # 作者
header-img:  img/4_1/city2.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 心得
     - 常用
     - 论文阅读
     - 综述
---

# 石墨文档List

* [GATES Related](https://shimo.im/sheets/T3Cp36PRHPj38TKp/MODOC)
* [NAS Reading List](https://shimo.im/sheets/T3Cp36PRHPj38TKp/MODOC)



# Paper Stack 文章堆栈

### GNN

* Foundation

### NAS

* [Transfer Learning with Neural AutoML](https://arxiv.org/pdf/1803.02780.pdf)
  * Parallel Training on Multi-tasks, Transfer the search strategy
  * 当有了一个pretrained的controller的时候，只有task embedding需要更新
  * Generic SS (Learned task representation & Task-specific advantage normalization)  
  * In standard single-task training of NAS, only the embedding
of the previous action is fed into the RNN. In multitask training, the task embedding is concatenated
to the action embedding. We also add a skip connection across the RNN cell to ease the learning
of action marginal distributions

* [MetAdapt: Meta-Learned Task-Adaptive Architecture for Few-Shot Classification](https://arxiv.org/pdf/1912.00412.pdf)
  * 用MetaLearning+NAS来搜索出更适合Few-shot的架构
  * Differentiable-NAS
  * 发现FewShotLearning的backbone model是先越大越好，然后会差
    * 认为D-NAS可以mitigate overfitting
    * Follow DARTS - Arch as DAG
  * 与一半NAS不同，不是找到一个架构用
    * 找到能更快adapt到别的task的
    * 有一系列small networks,MetAdapt Controller，同时更新weights，修改connection
  * FewShotLearning
    * Metric Learning based
      * Learn a non-linear embedding into a metric space, use L2 distance for classification
        * (Some change L2 disatnce with a implicit learned function/GNN)
      * Could improve when adding semantic information
    * Generative Based
      * 或者说是augmentation-based
      * 设计到一些transfer相关的一些东西
    * Meta Learning - learn a learning stragegy easily adapted to other few-shot tasks
      * trained on a set of few-shot tasks (episodes)
      * 另外一支是gradient-based
        * MAML
        * MetaSGD
    * MetAdapt的flow
      * arch as DAG , feature map as node / op as edge
      * Task-Adaptive Block
* 这个图的配色不错
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200313202441.png)

* [Channel Gating Neural Networks](https://papers.nips.cc/paper/8464-channel-gating-neural-networks.pdf)
* 思想来源于Feature Map中有很多绝对值小的地方，这些东西其实是可以不需要的
* 核心观点在于，认为partial sum和global sum是有相关性的(成立的前提假设-做了实验一半的channel有0.86的相关性)，所以partial sum发现不effective的部分没有必要继续做计算(以一定condition)
  * 这个threshold是硬的
  * 可以fine-grain也可以channel-wise
  * 把所有的W_l分一部分W_p和另外的W_r,它们的结果应该在这一层是要加起来的，把W_p* X_p的输出(Partial Sum)输入一个Channel Gating模块，以一个threshold来确定是否需要对剩下的W_r Apply Mask，给出一个Out_C维度的Mask，选择的依据是Feature Map的Activation绝对值
  * 相当于是一个新的layer type
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200313154531.png)
* 有一个channel grouping确保all channels are treated equally
  * 相当于防止每次都把前几个channel做为W_r，所以加了一个channel shuffling
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200313162432.png) 


* [Review Netowrk Pruning via Transformable NAS]()
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200317204835.png)
  * 用NAS来处理Channel Pruning的问题
  * 每个layer的channels，首先需要设计一个distribution，用类softmax的方式来，决定选择每个channel的概率
    * 选取channel的过程本身不differntiable，用gumble-softmax来
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200317203915.png)
    * CWI (Channel-Wise Interpolation) -  Adaptive Average Pooling
  

* [HAQ: Hardware-Aware Automated Quantization with Mixed Precision](http://openaccess.thecvf.com/content_CVPR_2019/papers/Wang_HAQ_Hardware-Aware_Automated_Quantization_With_Mixed_Precision_CVPR_2019_paper.pdf)


* [NAO]
    * Discrete -> Continuos 
      * 包含了一个encoder，将arch映射到一个连续空间，同时还搭配一个decoder
      * 还有predictor 
    * 与此类似的是DARTS，说DARTS认为最好的arch是当前weight下的argmax，而NAO直接用一个decoder映射回模型
      * 还有一支是Bayesian Optimization，作者认为GP的性能于Covariance Function的设计强相关
    * Search Space设计
      * 两步，首先决定1）which 2 previous nodes as inputs 2)确定要用什么op
    * symmetric的design:为了保证symmetric的模型（实际上是一个模型）的embedding一致，predictor给出差不多的结果
      * 用了Augmentation（flip）来训练encoder
    * Encoder和Decoder都是LSTM，predictor是一个mean-pooling加mlp
    * 三者Jointly Train
      * 认为predictor could work as regularization去避免encoder只对应decoder的结果，而没有正常表征
        * 这一步和传统VAE中的加noise一致
    * 认为weight-sharing和NAO是complementary的

* [SemiNAS](https://arxiv.org/pdf/2002.10389.pdf)
  * 用Controller(其实是里面的predictor)去在大量无标注的arch上做标注(不经过Evaluation)，将新的Data-Pair加入训练
  * 卖点是能够更快的找到比较好的架构，比EA之类的效率更高
    * 但是高的也不是很明显，怀疑是否是NAO带来的而不是Semi带来的(毕竟用自己推断出来的数据来训练自己(但是flow与Self-Supervised又不太一样))
  * 用NAO as example（因为它既可以用在conventional train from scratch也可以做Weight sharing）

---

* [MAML - Model Agnostic Meta-Learning for Fast Adaptation]()
  * Learn2Learn
  * 对标的是最经典的从pretrain model的transfer learning (严格来说Fintune也是一种Meta-Learning)
  * MAML还关注着多任务(与MultiTaskLearning有许多的关联)
    * 比如人脸关键点和定位是同样的一个问题，但是对NN来说是作为两个问题来处理的
    * MAML是以一个Task为基本单位的
  * 最终目的是**学一个好的初始化参数**
  * 新的Loss Function
    * 一部分loss是经典的loss，另外一个部分其他任务的损失参数
    * 有一个思想是不找单个任务的全局最优(Global Minima)，因为这样可能很难收敛到别的task的好结果，而是找到多个task之间的一个平衡(也不用最好)
    * 而传统的finetune首先是从任务A的最佳解出发，所以不一定对第二个任务效果好
  * 操作
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200317213623.png)
    * 我们先有了随机初始化的网络theta_0,在第一个task上按照正常的sgd训练获得参数theta_m,然后与此为基础获得valid loss，并且用这个梯度，不去优化theta_m而是去优化theta_0,去得到theta_1.下面再以theta_1作为上一步的theta_0，去apply到下一个任务（*这样去经历A String Of Tasks-有点像一系列课程*）

---

* [MixMatch]()
  * 用Autoencoder来重建输入图像，来获得好的Representation(或者是用GAN)
  * Semi较为有效的几个方案
    1. Consistency Regularization
    2. Entropy minimize： 来源于共识，决策边界不应该穿过边沿分布的高密度区域(Push Back Decision Boundary)吸引对未标记数给出低熵的预测
       * 对当前的结果肯定，所以有人用带Temperature的CrossEntropy 
    3. 最基础的L2正则化(在SGD下等价于Weight Decay)
       * 有说法说Adam和L2正则一起作用会出现区别
    4. 一种新方法是MixUp，任意抽取两个样本，构造混合样本和标签
       * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200321202307.png) 
    5. MixMatch

* [En-AET]()
  * 用一个VAE去找到最好的transform组合
    * encoder找到一个好的embedding，decoder re-parameterize出transform

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200321203810.png)

* 对于在NAS中利用Semi的几个问题
  * 首先是两个维度，是在predictor找arch的时候用semi，还是直接找一个更适合semi的网络
  * 第一个维度看上去问题比较适合，实际不然
    * 但是在NAS Predictor中怎么利用unlabel的arch数据，感觉值得研究
  * 后者的motivation我还不是很确定，而且看上去有点困难
    * 架构对semi训练的影响？ fewshot别人其实还是说明了这个问题的
    * evaluation过于noisy，不能很好的衡量，很大程度上取决于训练的Trick
  * 1） 算法上是否对架构有影响
  * 2） 有一个新的任务，在线处理这些标注数据； 要说明别的任务上的架构不能work
 



* [Data Efficient Image Recognitio with Contrastive Predictive Coding](https://arxiv.org/pdf/1905.09272v2.pdf) by DeepMind
  * 将Unsupervised领域的Contrastive Predictive Coding的方法改进(revisit)并且加入到Semi当中
  * 目前Semi的ImagenetSOTA / Also work on Det (PASCAL_VOC 2007)
  * CPC - Contrastive Predictive Coding
    * Learn Representation
  * Workflow
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200326142002.png)
    * 1. Unsupervised Spatial Prediction
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200326153411.png)
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200326153436.png)
      * 首先将图片分为多个有Overlapping的Patch，构成一个Grid
      * 经过一个前景Feature ectractor,最后接一个Mean-Pooling，保证每个Patch对应一个Feature vector
      * 之后再经一个Context Network，目的是Recognize出输入的Embedding(Feature Vector)是来自哪个Grid Cell
        * ContextVector输入的是一系列Zij(其感受野是所有在它之前(↖)的Zij，输出是一个context Vector cij
        * 这里训练中用到了InfoNCE Loss，改进自NCE(NoiseContrastiveEstimation)最大化cell以及其来自位置的mutual info，就是从Z_l中采样出一些z，只有zij match了才是正样本
        * Encoder和Context Network一起训练  
        * **这一步的核心思想是利用之前的信息去预测之后的信息，ContextVector-Cij中包含了ZiJ+的信息，但是其不是输入**
          * 以此去获得更加Consistent的Feature，就是Time(这里是Location-Invariant的Feature )
    * 2. ClassificationTask
      * 后面的ContextNetwork不要，直接利用已有的Label按照Supervised的方式接一个分类网络(甚至可以是一个简单的Logistic Regression)

* 上文的前身[Contrastive Predictive Coding](https://arxiv.org/abs/1807.03748)
  * [Post](https://mf1024.github.io/2019/05/27/contrastive-predictive-coding/)
  * Unsupervised Training Feature Extractor for Representation
  * 思想是利用Prediction作为目标(objective)
    * 常见的思想是Training the model to predict Future / Some Missing Information(对于上文来说就是Patch对应的Localization的Info)
      * 这个思想是否与VAE或者是GAN的思想类似？
    * 利用这个Prediction问题所提取的Encoder
  * 在采出的一个正样本和一系列负样本中，让预测结果去贴合正样本，最小化的是互信息量/或者是KL散度(类似这样的方式)
  * *这篇文章本身不止说Vision(还有NLP和RL)，上面那篇是只针对Visual*

* 相关的最近的paper还有[A Simple Framework for Contrastive Learning of Visual Representations](https://arxiv.org/pdf/2002.05709v1.pdf)
  * Hinton做的东西
* [S4L-Self-Supervised Semi-Supervised Learning](https://paperswithcode.com/paper/190503670)
  * ICCV2019 - Google




* [BigBiGAN - Large Scale Adversarial Representation Learning](https://arxiv.org/pdf/1907.02544v2.pdf)
  * DeepMind
  * 指出了近年来Self-Supervised的方法逐渐打败了基于Adversarial的，本文着手于用Adversarial learning来改进Representation
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200326155430.png)
  * 之前的工作BigGAN和ALI(Adversarial Learnt Inference)其flow一般为:
    * 我有Data X服从分布P_x，还对latent Variable Z有一个分布P_z(作为prior一般是gaussian)，Generator建模P(X|Z)-也就是从分布P_z中采样出Latent Variable Z，然后还原回X；对于传统的GAN，有一个Discriminator(Encoder)建模相反的概率分布P(Z|X),给定数据，predict这个embedding(\epsilon)
    * BiGAN(bidirectional)加入了一个Joint Discriminator,输入的是一个(X,Z)的Pair，需要分辨的是它们是来自Sample&Encoding还是LatentSample&Generator
      * 这样看来Generator的目的是“Fool”Disciminator by 让两个联合分布更加接近P_x\epsilon & P_zG（两者之间的距离用了一个Jenson-Shannon Divergence）
    * 一个有趣的训练结果是，当G/D都是确定的函数的时候(两个分布是狄拉克分布的时候)它们两个在Global Optimal点上是互为相反值
  * 本文发现修改了Discriminator能够在不compromiseGenerator的情况下提升性能
    * Disciminator有三个组件FHJ，F的输入只为X，J的输入只为Z
  * 实际做分类的时候是先Unlabel的训练一个BigBiGAN，然后Freeze Representation，训练一个Linear Classifier在有label的数据上展开


---


### 2020-03-17 MCMC

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200317151113.jpg)
* 撒豆子算面积的实验


### 2020-03-20 DeConv
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200320185002.png)

### 2020-03-24 Gumble Softmax
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200324113027.png)
* 从VAE(连续分布采样的求导)，加入一个Gumble噪声(包含了参数的信息)，首先从Gaussian中采样，带入值
* 对于离散分布，加一个Argmax->Softmax 


### 2020-03-26 Self-Supervised Learning

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200325231129.jpg)
* [MoCo](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/1911.05722.pdf) by Kaiming
  * 将Contrastive Learning的问题抽象为了一个Dictionary Learning的问题
    * 维持一个Dict其中有一些Encoding，当进入一个新的Sample的时候，经过这个Encoder，我需要它能从这个Dict中Query出一个与其最类似的Key，这样就有两个问题： 1. dict要足够大 2. Dict要具有一致性
    * Similarity是用InfoNCE(a kind of contrastive Loss Function)
    * Dict中的Keys是On-the-fly被定义出来的(Momentum Encoder 给出的结果)
  * 如何判定是不是同类别？InstanceDiscrimination:
    * 该Sample与其他的所有图片都是异类，对其本身Aug出的结果认为是同类
  * Inference的时候后面直接跟一个1层的fc，加一个softmax
* CPC - CPC通过对多个时间点共享的信息进行编码来学习特征表达，同时丢弃局部信息。这些特征被称为“慢特征”

---

### 2020-03-27 

* 生成模型的几种流派
  1. AutoRegressive 
  2. VAE
  3. GAN
  4. FLOW（NICE）
* 都是通过随机噪声来生成数据，在建模分布的时候，都是通过度量噪声与训练数据的分布差异
* 不同点
  * VAE的z到x的Dependency是一个随机的过程，而GAN是一个确定的，因而VAE的Inference是Well-Defined，而GAN的就相对病态了
  * GAN是为了生成新图片，VAE除了这个之外还需要建模数据的分布以及HiddenState
  * 流模型直接从原始问题出发，直接建模训练数据和生成数据的概率关系，用可逆推的NN来训练，z与x的关系是一一对应的
  * 最直观的反应是Loss不同
    * VAE是ELBO(最大似然估计-等价于最小化KL散度-注意KL三度不是在数据和噪声之间的，而是模型的p(x)与数据的p(x)之间的)
      * ELBO是Likelyhood的Lower Bound
    * GAN是最小化Jenson-Shannon Divergence，这个也是Model给出的P(x)和数据给出的P(x)之间的(**说人话就是两个Embedding之间的**)
    * FLOW用的也是最大似然估计，由于用的是可逆的NN



