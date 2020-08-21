---
layout:     post                    # 使用的布局（不需要改）
title:      Paper Stack 文章堆栈           # 标题 
subtitle:   待读的文章和无处安放的文章解读   #副标题
date:       2020-08-06            # 时间
author:     tianchen                      # 作者
header-img:  img/7_1/3-build.jpg  #这篇文章标题背景图片  
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


* [Convolutional Networks withAdaptive Inference Graphs](https://arxiv.org/pdf/1711.11503.pdf)
  * 挺有意思的一篇文章，思想是CNN不需要一直FeedForward下去、
  * 以一个Adaptive Inference Graph的思想
  * Robustness角度也能更好

* [Learning multiple visual domains with residual adapters](https://arxiv.org/abs/1705.08045)
* 🔑 Key:    
  * 通过一个Residue的Adapter，学习多个Task的Share Representation
* 🎓 Source:  
  * VGG-Oxford   NIPS2017 
* 🌱 Motivation: 
  * 传统的方法对不同的Problem/Data,学习不同的Representation，本文目的是学习一个Representation去处理多个任务
  * Tunable Network Structure，通过一个Adapter Residue Block，可以对不同dataset on-the-fly的调整  
* 💊 Methodology:
  * 对Data-dependent Learning问题，可以抽象为用一个Auxiliary网络来从Input Data，Predict Domain，domain specific的param就可以用一个网络来predict出(相当于去Parameterize这些domain-specific的param)
  * 本文认为线性的对filter param做parameterization等价于加入一个新的中间层，这样就可以显式的区分开Domain-specific和domain-invariant的feature了
  * 所以引入了一个Adaptive Residue Block
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200415131036.png)
    * 其中\alpha被限定为1x1Conv
    * 在Adapter1x1Conv之前有一个BN，其scale与bias也是domain-specific的
    * 对比了一下domain-(in)variant的data比例
      * 2(h^2C^2+hC)个无关的/2(C^2+5C)个有关的(h是kernel size，一般是3)
  * 传统的Multi-Domain Learning用finetune的方式会陷入Catastrophic Forgetting
    * 本文首先在一个大的domain(比如Imagenet)上训练domain-invariant的，然后再只tune domain-specific的
* 📐 Exps:
  * Different Visual Tasks
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200415115730.png)   
    * FGVC-Aircraft： 100Imagex100Class    
    * DPes: 50,000 Greyscale Pedestrain [18*36]
    * DTD: Texture,5640image,47class
    * GTSR: Traffic sign 43 class
    * Flower102: 102 Class of Flowers (40-258 image per class)
    * ILSVRC12: 1k Class, 1.2M
    * Omniglot: 50 class, 1623 Handwritten character
    * SVHN: 10 class, 70,000 real world sign
    * UCF101: action-recog 101 class 113,320 videos（对本文来说，对视频做了Dynamic Image Coding，将整个video encode成了一张图）
  * Metric是decathlon discipline
    * (需要在所有task上效果好)
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200415133227.png)
* 💡 Ideas:  
  * Different Domain Share Low/Mid level patterns     

---

```
* 🔑 Key:   
* 🎓 Source:  
* 🌱 Motivation: 
* 💊 Methodology:
* 📐 Exps:
* 💡 Ideas: 
```


---


* [DetNAS: Backbone Search for Object Detection](http://arxiv.org/abs/1903.10979)
* 🔑 Key:   
  * nas 4 Det backbone
  * Supernet
* 🎓 Source:  
  * Megvii
* 🌱 Motivation: 
  * Det often needs imagenet pretraining & NAS requires accuracy as supervised signal
    * Imagenet-pretraining + Det finetune
  * Pre-training and finetuning are costly
    * Following One-shot, decouple the weight training and the architecture
  * Det task perf. as guide, to search for the Backbone Feature Extractor
* 💊 Methodology:
    * Steps: 
      1. SuperNet Pretrain on ImageNet and finetune on Det Task
      2. NAS on Supernet with EA
        * Path-wise(at one time, only updating samples path)
    * Finetuning BN
      * Freezing BN(Traditional) wont work for supernet(normalize couldnt acquired at different paths)
      * SyncBN replace regular BN
        * Compute BN Statistics across multiple GPUs (save memory consumption)
      * Also when EA searches arch, each BN param should be independent
        * need to re-accumulate the BN for every new arch
    * SS Design
      * Small/Big (40/20 Blocks)
        * Small used in Ablation Study
      * based on ShuffleNetV2 Block(involves channel split and shuffle operation)
        * 4 Choices:
        * x3: kernel-size [3,5,7]
        * x1: replacing right branch with Xception block(3 repeated DW 3x3 Conv)
        * 4^(40) choices for big ss
    * EA
      * Mutation + CrossOver
      * arch dont meet constraint will be removed when updating
* 📐 Exps: 
* 💡 Ideas:
  * Det's Direction
    * Architecture: FPN - Top-Down arch with lateral connection, integrating features at all scales
    * Loss: RetinaNet's Focal Loss, dealing with the class imbalance(Instability in earlier training?)
    * MetaAnchor: dynamic anchor mechanism
  * AmeobaNet - plain EA without Controller could also achieve
  * Det often uses the image classification backbone which could be sub-optimal(DetNet59 > ResNet101)
  * NAS-FPN only searches for the Feature Pyramid Network
  * EA could better handle Constraints than RL & Gradient-based 
  * [SyncBN](https://tramac.github.io/2019/04/08/SyncBN/)
    * Plain BN with DataParallel - Input distiributed to subsets and build different models on different GPU
    * (Since independent for each GPU, so batch size actually smallen)
    * Key2SyncBN: Get the global mean & var
      * Sync 1st then calc the mean & var
      * (Simplest implemention is first sync together calc mean, then send it back to calc var) - Sync twice
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200418111031.png)
        * modify computation flow and only sync once
    * [Code](https://github.com/tamakoji/pytorch-syncbn)
  * ------------------------------------------
  * Rather Small SS
  * Simple Shared-Weights with interesting handle of BN


* [NAS-FPN: Learning Scalable Feature Pyramid Architecture for Object Detection](http://arxiv.org/abs/1904.07392)
* 🔑 Key:   
  * Search a FPN(Feature Pyramid Network) in a ss covering all cross-scale connections
* 🎓 Source:
  * Quoc V Le Google  
* 🌱 Motivation: 
  * Huge design space(increase exponentially)
* 💊 Methodology:
  * Following Cell-based SS(author called it as scalable architecture), main contribution is designing **search space**
    * the SS is **modular**  (Repeat the FPN N times then concat into a large net)
  * RNN RL Controller
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200418171859.png)
  * Feature Pyramid Network
    * (following RetinaNet, use last layer in each group of feature map as *the input of the FPN*)
    * 5 Scales(C_{3,4,5,6,7}) stride of 8/16/32/64/126 pixel（6,7 purely max-pooling of 5） for Merging Cell
    * Composed of multiple merging cells  
    * Input/Output same size - can be stacked (num to stack would control the acc/flops trade-off)
    * Each merging cell gives output are appended into the candidate layers, also feeds into next merging cell
      * Finally the 5 merging cells are output 
  * Merging Cell
    * basic element of FPN
    * Merging 2 input feature map of different size
    * Each Cell has its own resolution(the output)
    * 4 Step: 
      1. choose input-layer-1 
      2. choose input-layer-2 
      3. choose output feature resolution
      4. select binary op(add or maxpool) - (scales are handles b4 the binary op)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200418173400.png)
  * Meshgrid Representation
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200418173458.png)
* 📐 Exps:
  * Scalable: Stacking NAS-FPN blocks could improve acc while stacking simple block couldnt
* 💡 Ideas:
  * FPN - fuse features across different scales (cross-scale connection in ConvNets)
    * Deeper layer feature semantically strong but less resolution, are upsampled and added with lower-representation for feature with both good semantic and resolution  
    * sequentially combining 2 adjacent layer feature (with top-down/lateral connection)
    * RW
      1. [Path aggregationnetwork for instance segmentation. In CVPR, 2018.]() - add an additional bottom-up pathway
      2. [M2det: A single-shot object detector based on multi-level feature pyramid network. AAAI, 2019.]() - Multiple U-shaped Modules
      3. [Deep feature pyramid reconfiguration for object detection. In ECCV, 2018]() - Combine features at all scale + Global attention
    * Problem: manually designed and shallow(compared to backbone)
  * Any-time detection: dont necessarily need to forward all pyramid networks
    * Desired when computation effort is concern
  * ----------------------------------------------------------------------------------
  * Google's Work, really strange Hyper-param（lr-0.08/8 epochs training） (Maybe Grid-Search Again?)


* [EfficientDet: Scalable and Efficient Object Detection](http://arxiv.org/abs/1911.09070)
* 🔑 Key:   
	* Systematically NAS for Det Task
  * Combining EfficientNet Backbone + Bi-FPN + Compound Scaling
* 🎓 Source:  
	* Quo V Le Google Brain
* 🌱 Motivation: 
	* Weighted Bi-directional FPN - for multi-scale feature fusion (Better Feature Aggregation)
	* Compound Scaling method - uniformly scale the resolution/depth/width (Scalable)
* 💊 Methodology:
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200418223126.png)
  * the "BiFPN", analogy of FPN & PANet
    * from traditional "Top-down" structure(1-way information flow)
    * Adding an extra edge when I/O is at the same level
    * remove node with only one input edge 
    * Adding Weighted Feature Fusion - like Attention
    * exponential scale up BiFPN width
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200418220047.png){:height="100" width="100"}
* 📐 Exps:
* 💡 Ideas:
  * One-Stage Det: (Anchor-Free) whether have a region proposal step

---

* [Learning to Remember: A Synaptic Plasticity Driven Framework for Continual Learning](http://arxiv.org/abs/1904.03137)
* CVPR2019
* Arch:
  * Conditional GAN with learnable neural masking
    * During SGD Add Mask on Layer/weight
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200420174733.png)

* [Lifelong Learning with Dynamically Expandable Networks](http://arxiv.org/abs/1708.01547)
* ICLR2018
* Arch:
  * Add Neurons by heuristic rule(Add and re-intialize new neurons)

* [An Adaptive Random Path Selection Approach for Incremental Learning](http://arxiv.org/abs/1906.01120)
* NIPS2019
  * Dynamic adaptive path selection
  * Attention-based on Peak Path Response
    * Paths Re-weighted with new task 
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200420185856.png)

* [Memory Aware Synapses: Learning what (not) to forget](http://arxiv.org/abs/1711.09601)
* ECCV 2018
  * Memory-Aware Synapses: Marking the parameter of a NN online and unsupervised

* [IL2M: Class Incremental Learning With Dual Memory](https://ieeexplore.ieee.org/document/9009019/)
* ECCV 2019
* Dual Memory 1. for examplers 2. initial class statistics(class predictions) - used for rectify the classification(deal with imbalance)

* [Piggyback: Adapting a Single Network to Multiple Tasks by Learning to Mask Weights](http://arxiv.org/abs/1801.06519)
* ECCV 2018
  * Learn Multi-task Masks  
  * Task-specific loss train the soft threshold
    * one mask per task
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200420192129.png)

* [Learn to Grow: A Continual Structure Learning Framework for Overcoming Catastrophic Forgetting](http://arxiv.org/abs/1904.00310)
* ICML 2019
  * decouple the arch learning and param learning
  * Growing the network-NAS-like DARTs
    * Search for block (new/reuse/adaption)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200420202125.png) 

* [Efficient Lifelong Learning with A-GEM](http://arxiv.org/abs/1812.00420)
* ICLR2019
  * Evaluating Efficecny of lifelong learning methods (metric for measuring how quickly learning)
  * Improve the original GEM(Gradient Episodic Memory)
    * Distribute an episode memory
  * mainly discussed why other methods fail to fitting the "Lifelong Learning" because not dealing with a single pass of the stream of data


* [Learning without Memorizing](http://arxiv.org/abs/1811.08051)
* CVPR2019 2019
  * no stroing data sample for old task
  * change in classifier attention map for old tasks
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200420204205.png)

* [Lifelong Learning with Searchable Extension Units](http://arxiv.org/abs/2003.08559)

* 🔑 Key:  
  * * Lifelong Learning predefine arch, this method search for extension model
* 🎓 Source:
  * arxiv 2003 & ZJU 
* 🌱 Motivation: 
  * Summarize the lifelong learning
    1. Fix methods: retaining data information of previous task
      * Often encounter capacity bottleneck
    2. Expansion Methods: 
      * Newly added params are added, though the old are frozen or regularized
      * Then re-train whole/subnet for new task
      * May Increase rapidly(redundant)
      * Also added elements are constrained to be the same with the previous
* 💊 Methodology:
  * Contains: 1. Unit Searcher   2. Task model creator
    * Super Model(Contain Multihead model)
    * Task Model(Part of the super model,all has the same layer)
    * Each task has task-specific output layer, other layer are shared (composed of many EUs)
  * Proceudre （seems pretty plain for NAS...）
    1. Search EU - in current task
    2. Expand the SuperModel: adding EU in each intermediate layer
    3. Create Task Model: Select the best EUs(part)
    4. Train the task model, train the net
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200420213758.png)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200420215011.png)
* 📐 Exps:
* 💡 Ideas: 
  * new metric named mixed score: measuring both param size and accuracy of the life learning problem
---

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

---

### 2020-04-05

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200405093121.jpg)

---

### 2020-06-17


- [Searching for Accurate Binary Neural Architectures]()
* Huawei Noah
* only search for width(channels), acquire higher acc with less flops
* the arch remain the same with the original fp32 model


- [Learning Architectures for Binary Networks]()
* GIST(South Korea)
* seems like eccv ...
* cell-based, proposed a new cell template composed of binary operations
* novel-searching objective - Diversity Regularization  
* 这些new ss基本都是一个套路，对dw separable，dilated conv binary化
  * 这篇文章里多了一个zerorise layer(没搞懂)，说是作为placeholder

- [Binarized Neural Architecture Search]()
* Beihang Univ
* Darts foundation
* channel sampling / operation space reduction
  * abbadon less potential operation

- [BATS: Binary ArchitecTure Search]()
* Cambridge
* binarized ss
* search strategy



