---
layout:     post                    # 使用的布局（不需要改）
title:      Survey About Incremental Learning               # 标题 
subtitle:   Make Learning Able Again!     #副标题
date:       2019-09-06              # 时间
author:     tianchen                      # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - DL
    - Survey
    - Incremental Learning
---
# 定义
* Life Long & Online Learning & Incremental  核心在于**可继续的学习**后者加上了在线更新以及内存受限等限制
* Transfer Learning & Multitasking & Few-shot Learning  主要处理的是数据差异大的问题
* Continual DNN时代IL的阐发  更加广义地解释IL

* What's Different In **Incremental Learning**?
    * Dynamic
    * Use Data Streaming (Has Internal Temporal Order) - Could Be Used (But Recent Methods Never Used)
    * Adaptive Model Complexity - 例如SVM中的SV数目，NN中的 Hidden Unit数目运行中改变
    * 其训练本质和Stochastic的训练方式类似（SGD,especially Batch），区别在于Hyper-param是依据整个数据分布设定的，而这里数据分布未知且变化（Concept Drift）

# 应用场景  
* **使模型更加贴合用户的使用习惯**

* 终端     
    * No Connection -> Protect Privact 
    * Robotic（Auto-Driving） / IOT设备
* 云端（Big Data） (上线之后的数据和训练数据差距大们需要Adaptive)
    * 推荐系统  
    * 数据降维表征处理 Feature Extraction：PCA，Clustering

# 主要问题（研究方向） *Ranked By Importance*
（由于具体的处理方法因对应算法常有改变，本部分的Solution以解决问题的思想为主）
## Concept Drift
* 最主流解决方案： Ensemble
* Incremental Learning 中的主要问题之一（有时也别描述为 Unstable-Data ）主要可以分为两类： Virtual & Real
    * Virtual: 输入数据的Distribution发生改变
        * 与 Class Imbalance 相关   
            * *采用 Importance Weighting 的思想，对新输入的数据做特殊处理*
        * 当新 Class 会引入之后，输入数据会突变 —— Novelty（奇异）
            * *引入 Novelty Check模块，针对性解决问题*
    * Real: The P(Y|X) Changed
        * Statistically Detect CD(偏硬核统计方法) - Hoelffding Distance(不太有后续)
        * Ensemble Model(集成学习) 融合不同的分类器 （感觉是靠Robustness硬扛）  
        **目前最有效的处理Concept Drift的方法** 

## Catastrophic Forgetting
* 最主流解决方案： 加强学习规则 （Enforce Learning Rules）- Explicit Meta Strategy
* 由于模型的计算量不能无限增大，所以Incremental Learning在接受新知识的同时，也有一个忘记旧知识的过程。“学的越快，忘的也越快”，这里存在一个权衡。（有时该问题也会被叫做 Stability-Plasticly Dilema）
* 目前深度学习中的Catastrophic Forgetting貌似含义和IL中的不太一样，可能有时候更为广义,训练数据和上线之后的实际数的差异大的问题也属于此类,我理解是NN泛化能力不足,Incremental是解决它的方案之一.
    * 在NN中的主流解决方案:    **在Loss中加项去"蒸馏"出老任务的信息并加以保留**
* 在如NN等Connetionist类的模型中比较严重
    * 在新时代处理方式比较多样，在NN领域 有一篇[综述文章](https://openreview.net/forum?id=BkloRs0qK7)介绍

## Memory Bottleneck —— Efficient Memory Model
* 由于存储有限，所以需要尽量的压缩输入数据 
* 主要分为两类 数据表征Explicit/Implicit
    * Implicit （Example/Prototype Based Methods： Combine Human Coginitive Categories & Numerical Features ）
    * Explicit (经常采用短期记忆的方法，只保留一段时间)

## Meta-Parameter Tunning
* 由于输入数据Unstable,导致超参数是变量
    * 让模型更加Robust
    * 采取Meta-Heuristic的方法去调整超参数（比较困难）

# Benchmark
> 由于该领域较为宽广，处理的问题与使用算法的跨度都比较大，导致没有一个公认的指标（类似Image Classification 的ILVSR）
同时，衡量IL的算法的指标维度较多，有一篇[文章](https://www.sciencedirect.com/science/article/pii/S0925231217315928)对不同算法进行了比较好的对比(对比了截止2018年的各种SOTA的对比，但是不太有NN相关的)

* 主要Evaluate的维度有
    * Accuracy
    * Converge Time
    * Complexity
    * Hyper-Params Optimization

> 在NN领域,有另外一篇[文章](https://github.com/GMvandeVen/continual-learning)将IL的主要任务进行了区分,并且介绍了后NN时代的主要算法在不同场景下的效果.

# New Popular Methods
1. LWTA(Local Winner Takes It All) - As An Activation Function     *(A little Outdated)*
2. EWC(Elastic Weight Consolidation) - As Regularization In Loss Function
3. ICaRL(Incremental Classification and Representation Learning) - A Structure Of Representation Learning Using CNN
4. LwF(Learning Without Forgetting)
5. DGR (Deep Generative Replay)


# 推荐文章
## 1. Incremental On-line Learning: A Review and Comparison of State of the Art Algorithms  （A Survey On Pre-NN SOTA Methods For IL）
* [Link](https://www.sciencedirect.com/science/article/pii/S0925231217315928)
* Cite：33
* Rank :   :star::star::star::star:
* INTRO:   对前NN时代的IL的SOTA方法进行了多维的可靠对比,同时梳理了他们用到的方法
* Digest: 

## 2. Incremental learning algorithms and applications (Old Survey Of The Field Of IL 2015)
* [Link](https://hal.archives-ouvertes.fr/hal-01418129/)
* Cite：62
* Rank : :star::star::star:
* INTRO: 对整个领域的概况做了详细的梳理，也列举了很多参考文献；缺陷在于年代过于久远
* Digest： 

## 3. Interactive Online Learning for Obstacle Classification on a Mobile Robot 
* [Link](https://ieeexplore.ieee.org/abstract/document/7280610)
* Cite: 17
* Rank: :star::star::star:
* INTRO: 算是一篇比较完整的文章，多方考虑了各种问题（比如Memory Bound），最后有自然场景的应用落地，核心问题是图像的分类问题，采用了I-LVQ
* Digest

***
(以下的文章是与NN有关的IL相关文章,方法与以上的方法差距相对较大,但是思想类似)

* 整个领域的发展: 
    * 首先以2013年Bengio和Goodfellow那篇文章的研究开始,人们开始研究NN的CF(其实解决了就能Incremental了) 当时已经有了比如LWTA之类的一些尝试,但是不成熟
    * 主要流派,归纳来自于(3 Scenario一文,文中对他们的效果也做了对比)
        * Task-Specific Parts: 
            * XdG:
            * 对于每一个Task采取网络的一部分进行处理: (必须Task-Specified)
        * Regularized Optimization
            * EWC & SI
            * 可以处理Task Unknow的情况
        * Replay Training Data
            * Use Old Model To Create New Data's Presentation -- LwF
            * Generate Data To Replay - DGR
        * Store Old Data As Examples
            * ICaRL
    * 后面则是各种开花,大家提出各式各样的架构让各种网络Incremental(有些就很玄学了,基于Brain的啊,各种distillatoin的啊,还有Attention机制啊等一些比较花哨的Trick,但是主干基本上还是ICARL的Method)

## 4. An Empirical Investigation of Catastrophic Forgetting in Gradient-Based Neural Networks 
* [link](https://arxiv.org/abs/1312.6211)
* Cite: 200+
* Rank: :star: 
* INTRO: Bengio实验室出的一篇分析论文,主要指出了Dropout训练对于缓解CF的作用,领域开山,意义不是特别大,但是提出的分割数据集方法比较经典(利用Premute分割Mnist1为几个SubTask)
* [Code](https://github.com/goodfeli/forgetting)

## :star: 5. Learning Without Forgetting (ECCV 2016)
* [link](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8107520)
* Rank: :star::star::star::star::star:
* INTRO
    * 他们居然自己更新自己的工作还起了一样的名字? [Old Link](https://link.springer.com/chapter/10.1007/978-3-319-46493-0_37)
    * 2016年那一版是领域的开山之作,比较经典,应用面很广,不仅在IL领域,也涉及TF
    * 文章内容稍偏杂,分析对比也不止有Incremental Learning

## :star: 6.  iCaRL_Incremental_Classifier(CVPR_2017)
* [link](http://openaccess.thecvf.com/content_cvpr_2017/html/Rebuffi_iCaRL_Incremental_Classifier_CVPR_2017_paper.html)
* Cite: 172
* Rank: :star::star::star::star::star:
* INTRO: NN时期的IL的一篇起步的标准文章.采用CNN做Feature Extractor,每一类别给出一个Prototype-Vector以完成分类,给出了一个完整的Workflow,对比如Memory Bound等问题都有所考虑到,之后的文章多有引用
* [Code](https://github.com/srebuffi/iCaRL)   Tensorflow
* [Code](https://github.com/donlee90/icarl)   PyTorch  (这个代码里面有把cifar变icifar的实现)

## :star: 7. EWC - Overcoming CF In NN (NIPS2017)
* [link](https://www.pnas.org/content/114/13/3521.short)
* Cite: 541
* Rank::star::star::star::star::star:
* INTRO: Deepmind出品,提出了EWC,本质是修改Loss函数加上了约束,让网络在训练新任务的时候尽量保持对原任务重要的参数(有一个弹性机制),文章中对分类和RL任务都做了分析.可实现性强,开山文章,经典.
* [Code](https://github.com/stokesj/EWC)
* 后续还有一个Online的修改版本   [link](https://www.pnas.org/content/pnas/early/2018/02/16/1717042115.full.pdf)

## 8. Continual Learning with Deep Generative Replay(NIPS 2017)
* [link](http://papers.nips.cc/paper/6892-continual-learning-with-deep-generative-replay)
* Cite: 70+
* Rank: :star::star::star::star:
* INTRO: mit.paper,后续还有一个Distillation的[版本](https://arxiv.org/abs/1802.00853)(借鉴了LwF) 

## :star: 10. Three scenarios for continual learning
* [link](https://arxiv.org/abs/1904.07734)
* Cite: 12
* Rank: :star::star::star::star::star:
* INTRO: 将目前的IL的场景分成了三类,并选取了主流流派的几种方法进行了对比,还在附录详细讲解了每种算法的具体实现方式,并且有开源代码.(建议可以从这一篇看做综述开始读起)
* [Code](https://github.com/GMvandeVen/continual-learning)



## 其他文章
* A COMPREHENSIVE, APPLICATION-ORIENTED STUDY OF CATASTROPHIC FORGETTING IN DNNS (ICLR 2019)
    * [link](https://arxiv.org/abs/1905.08101) 
    * 后NN时代关于IL的一篇Survey，设计了一些Sequential Learning的场景（在Visual Classification任务上）介绍了目前NN领域IL的一些方法,没有上一篇3 Scenario 概括的好
* Incremental learning of object detectors without catastrophic forgetting (ICCV 2017; 50 Cite)
    * [link](http://openaccess.thecvf.com/content_iccv_2017/html/Shmelkov_Incremental_Learning_of_ICCV_2017_paper.html) 
    * Object Detcetion的一篇文章
    * [Code](https://github.com/kshmelkov/incremental_detectors)
* End2End Incremental Learning(ECCV 2018; Cite 18)
    * [link](http://openaccess.thecvf.com/content_ECCV_2018/html/Francisco_M._Castro_End-to-End_Incremental_Learning_ECCV_2018_paper.html)
    * 在ICaRL基础上完成了End2End
    * [Code](https://github.com/fmcp/EndToEndIncrementalLearning) in Matlab...
* PackNet: Adding Multiple Tasks to a Single Network by Iterative Pruning(CVPR2018; 43 Cite)
    * [link](http://openaccess.thecvf.com/content_cvpr_2018/html/Mallya_PackNet_Adding_Multiple_CVPR_2018_paper.html)
    * 与DeepMind的EWC思路类似,利用Over-Param,通过迭代剪枝实现
* Piggyback: Adapting a Single Network to Multiple Tasks by Learning to Mask Weights(ECCV 2018;Cite 16)
    * [link](https://arxiv.org/abs/1801.06519)
    * 与上面一篇思路类似
* Reinforced Continual Learning(NIPS2018; 12Cite)
    * [link](http://papers.nips.cc/paper/7369-reinforced-continual-learning)
    * Rl的一篇文章
* Large Scale Incremental Learning (ICCV 2019)
    * [link](https://arxiv.org/pdf/1905.13260.pdf)
    * 比较新的一篇,比较大规模的做IL的
* Continual Learning in Deep Neural Network by Using a Kalman Optimiser
    *[link](https://arxiv.org/pdf/1905.08119.pdf)
    * 用卡尔曼滤波的思想去让不同的Param对应不同的Task
* Functional Regularisation for Continual Learning
    * [link](https://arxiv.org/pdf/1901.11356.pdf)
    * Regularization流派的最新论文,已经很偏数理了...
* Incremental Learning with Unlabeled Data in the Wild
    * [link](https://arxiv.org/abs/1903.12648)
    * 应用场景很美好,炫技偏多
* Continual Learning Using World Models for Pseudo-Rehearsal (2019.6)
    * [link](https://arxiv.org/abs/1903.02647)
    * Replay机制在RL领域的应用
* Learning to Remember: A Synaptic Plasticity Driven Framework for Continual Learning (2019.4)
    * [link](https://arxiv.org/abs/1904.03137)
    * Generative Replay 里面一篇比较炫技的文章
* LIFELONG LEARNING WITHDYNAMICALLY EXPANDABLE NETWORKS (ICLR 2018)
    * [link])(https://arxiv.org/abs/1708.01547)
    * 与主流流派不同的另一种处理办法
* Re-evaluating Continual Learning Scenarios: A Categorization and Case for Strong Baselines
    * [link](https://arxiv.org/abs/1810.12488)
    * 另外一篇总结,对比,尝试给出Baseline的文章(对比的方法不是很多,不是特别主流)
    * 提出了惊世骇俗的言论: Main Stream 的IL方法和Naive Replay和正则化甚至Adagrad相比没什么优势...
    * [code](https://github.com/GT-RIPL/Continual-Learning-Benchmark)
* 




# 资源
1. 他人总结的 Incremental Learning Reading List  [Awesome-Incremental-Learning](https://github.com/xialeiliu/Awesome-Incremental-Learning)
2. 比IL更广泛的Continual Learning Benchmark  [Continual-Learning-Benchmark](https://github.com/GT-RIPL/Continual-Learning-Benchmark)
    * 另外,这个组同时还写了 3 Scenario 和另外一篇IL的通用方法的论文,算是一个大组
3. 数据集 [CoRe50](https://vlomonaco.github.io/core50/benchmarks.html)
    * 很多论文中对数据集的处理方案还是用的Goodfellow文中创造SubTask的方案
4. 前NN时代的IL算法的源码和数据集整理 [link](https://github.com/vlosing/incrementalLearning)
5. :star: 最近比较流行的集中IL算法的实现与比较 [link](https://github.com/GMvandeVen/continual-learning)
    * 包括了 EWC,LwF,ICaRL等主流方法
    * 提到了3 IL Sceneerio Paper,对现有的方法做了一个对比
6. :star: 另外一个目前新兴的IL算法的复现代码 [code](https://github.com/arthurdouillard/incremental_learning.pytorch)

# PPT 
* 以一个概念介绍开始吧,辨析与其他学习关系,以三个特点结束
    * Transfer Learning 迁移学习
    * Lifelong/Online Learning 继续学习
    * 在NN领域,Incremental经常和Continual混用...(该领域也不太有特别严格的IL,主要是Stream Of Data难以满足)
* 在NN领域,以解决Image Classification问题开始, 从3 Scenario谈起
    * 3 Scenario: 主要基于Mnist (Permuted Mnist(给出几种固定的Permutatoin,创造出多个与Mnist原先的任务相同难度的任务) && Split-Mnist(10类分成5个二分问题))
        * TASK IL: 跨数据集的.比如先Mnist后SVNH,先ImageNet后PASCAL VOC(LwF)
        * Domain-IL  identical: permuted IL (不知道哪种Permutation)
            * 智能体环境决策
        * CLass IL: split IL (DGR)
            * 新的Classification
* TimeLine
    * 2013 Bengio/Goodfellow CF (Dropout)
    * 2016 LwF 
    * 2017 (DeepMind) EWC
    * 2017 ICaRL
    * 2018 DGR 之后基于现有算法做了各种优化
* 主要做这个的组,以及一些其他活动
* 两个流派 
    * Regularization based (训练技巧) 
    * Replay based (改结构)
* 3个方法的介绍
    * EWC:
    * LwF:
    * ICaRL:


# （个人思考）
* Incremental问题的思考
    * Incremetal常与CF相联系,但是CF并不只通过IL解决,同样也可以通过Transfer等方式解决.
    * 很多方法的设计依赖网络的Over-Param,与模型压缩矛盾
    * 克服CF的核心是如何保留住Old Task的Knowledge
        * 最Naive的方法就是在Loss上加一个L2范数 ||w-w0||
        * EWC就是在这个的基础上加上了Ealstic的机制,让对结果更重要的Weight去拥有更大的"弹性"
        * Advanced一点的方法主要涉及到Distillation
        * ICaRL的思路个人感觉更类似传统NN的方法,类似当时ISVM用的方法
            * NN其实只做了Representive Learning的特征(表征)选择器
            * 最核心的部分还是Prototype-Based(所谓的Weight Vector感觉和ISVM中的Prototype思想一致)的Example Set
* 数据的标注问题，训练需要有Label的数据
    * 在推荐系统等以Human FeedBack作为Label （在自动驾驶中其实也可以把人的行为作为FeedBack，不过没有那么直接）
    * FeedBack的Latency,数据进入之后需要完成动作才能有FeedBack。（其实对于训练来说个人感觉没有那么大所谓，就是对数据的储存提出了要求）
* 数据集的问题:
    * Ian GoodFellow的方法,对于一个数据集分出SubTask(利用Permutation)
    * iCifar 对原本数据集的修改
* 对于 A Stream Of Data的问题:
    * 一些算法更大程度上还是一些训练技巧(比如LwF,EWC)但是他们需要同一时刻所有的Training Samples Present
    * LwF文章里面也提及了,自己不能应对 A Stream Of Data Coming(感觉EWC同样也存在这一问题)
    * 目前主流的实现方案是 permuted Mnist,或者更换别的数据集以测试模型对New Task的能力,但是新数据集理论上也是以Mini Batch的形式进来(对终端板上训练能放多少东西存疑)
    * ICaRL理论上是可以处理Streaming Of Data的,但是其缺点在于2Stage,以及对所有样本求平均
* Memory Bound的问题:
    * 目前算法只要是Memory有Bound的都拿出来吹自己可实现,还没有落实到具体存储问题
* 后NN时代基本不太对 新的数据引入而重新设定Hyper-Param进行研究
    * 或许是因为比较耐挫,也或许是因为处理的任务差不多
        *(一些文章提及到当Task差距很大的时候,效果不好)

