---
layout:     post                    # 使用的布局（不需要改）
title:      论文阅读笔记《Billion-scale semi-supervised learning for image classification》           # 标题 
subtitle:   FaceBook的又一篇炼丹论文        #副标题
date:       2019-10-11             # 时间
author:     tianchen                      # 作者
header-img:  img/bg-luomu.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 论文阅读
---


# [Billion-scale semi-supervised learning for image classification](https://www.semanticscholar.org/paper/Billion-scale-semi-supervised-learning-for-image-Yalniz-J%C3%A9gou/88ee291cf1f57fd0f4914a80b986a08a90d887f1)
* FaceBook AI Research

## Abstract
* 提出了一种Workflow，**从大量的Unlabeled的数据当中训练**，采用了一个**TCH&STU架构**，提高了Image Classification的准确率
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925164124.png)
* 超级大量的Unlabel数据以及少量的Task-Specific带标注的数据
* **Flow**
    * 首先利用现有的带label数据首先获得一个Teacher Model
    * 利用训练好的TCH Model去给未标注的数据排序，获得一系列新的training data
    * 用这些数据去训练新的STU(一般比原先的网络小)
    * 最后用一开始的带Label的Data去finetune student network
* 可以避免class imbalance中的long-tail distribution问题
    * 作者的解释是：在原本的数据集中可能存在class imbalance，但是我们在构建新的Trainin Set的时候对所有Class取一样的Sample数，在大规模的unlabel data中会有一定数目的在原本数据集中比较少的类别对象
        * 可以调高Tail类(样本量少)的Recall率
        * 从一定程度上其实是大的数据集补充了原先lebeled set当中的tail class的样本数目，从low shot变成了正常情况
* 对一下几个Element Sensitive
    * Scale & Nature Of Unlabelled Data
    * TCh和STU网络的关系
    * 一开始Tch Model的准确度
* 整个流程可以理解为一种很Advanced的**Finetune**
* 获得的精度能够明显超过SOTA
* Flow：
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925191933.png)
    * 注意没有把新老的Training Set去Mix起来
        * 作者解释说那样的话会需要确定一个Mix的因数，所以直接就没有这样
    * 作者提到这里用KD效果会不好(因为我们要让student比teacher的效果好，所以就不能一味地模仿teacher)
* 一些Requirement 
    * **A Good Teacher Model Is Required** 需要用它来从新的Wild Data当中获得Label
    * **Student Model最后需要拿原先的数据来再Finetune**
        * 这意味着需要接入原先比如ImageNet的数据？需要解决
        * 对于NN的训练来说，每一个Mini-Batch的训练过程是否是独立？

## Experiments
* Models
    * Res18-Res50-ResNeXt101(很多不同宽度)
* 学生的Performance会明显超过老师
    * 这很合理因为它exposed to更多的数据
* 最后的Finetune过程非常关键
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925201749.png)
* 能获得比传统学习方式高的水平
* 作者另外还针对Self-Training进行了一个测试
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925203402.png)
    * 是否表示需要比较大的网络比较有提升的潜质(Res18就提升不上去了)
    * *作者表示Self-Training比较Risky，但是文章表明在最后加上利用GT数据的Finetune能够显著地提升效果*
* 对不同的Teacher架构，Student的表现(Stu都为res50)
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191012105608.png)
* 随着构建的新数据集大小越大，开始Performance有一个线性增长，后来趋缓
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191012105804.png)
* Performance随着Training Epoch数的增多而改变
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191012105931.png)
* Performance随着K值的变化
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191012110110.png)
    * 不同的结构对应最大值的K值不一样
    * 这里有一个最大值的原因是K越大，选进来的数据可能错误的越多(而更大的Teacher识别能力越强，能够兼容的K值越大)
* 分析了如果DataSet设计
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191012112841.png)
    * 作者认为Rank这一步起到了比较关键的作用
    * unbalanced会掉精度
* 前人的文章说明了图像的背景的task-related语义信息(文中叫meta-information)能提升performance(有点玄乎了啊就)
    * 作者管这种方式叫weak-semi-supervision，然后好像意思说这种方式不是特别work...
    * 可以认为文中的排序选择的方式，强行给新的数据集做了Denoise和Class Balance，所以才能更好...
* 
* 对参数的分析
    * K - Image Selected per Class
        * 16k/8k/4k分别对应100M,50M,25M
        * K值变大之后错误率会增加(显然)
    * P - being a parameter accounting for the fact that we expect only a few number of relevant classes to occur in each image
        * 一张图片最多能够被识别为P类
        * 选取P>1以在数据量不是很大的时候获得足够的Label
        * 实验中一般取P=10，能够获得一个几乎balanced Set


## Fracs
* Thinkin About Finetune： 当样本量不足以训练好一个网络的时候，用pre-train作为基础(防止网络去处理一些很复杂的特征提取之类的Dirty活)
    * 打个比方，刷BOSS赚钱的过程被替代了
    * 但是如果两个数据集的本质差距有点大的话就不太好做(比如当年做的雷达图像)
    * 冻结某些层去训练和finetune的本质无关 ~~我也不太清楚为啥有人会这么想~~
 * 而且这个和Incremental一个很大的区别就是，输入数据不是Streaming的，*是否需要保证把这些数据都记录下来？*
    * ❓一般的在线训练做的是什么？
    * 关键是这个东西所需要新的Set实在是大……需要超级多的存储，如果都存的话不大可能
* **如果我们已经有了一个好的模型，为何还要在线训练？**
    1. 证明可以提高*准确度*
    2. 可以利用新的Wild Data，Wild Data 可能和训练集当中有一定的差距，会*更加贴近现实情况*
        * 但是这种方法不能处理新的对象(也就是第一个训练集当中完全没有出现过的东西)
        * 一定程度上也是Transfer Learning(TL中有一个Example是DomainA是单纯的对象，第二个是在不同姿态下以及不同背景以及光照条件下的对象)
    3. 如果有Feedback的Label提供也可以反馈回来 
        * 比如可以有外部进入的Label也可以汇集到那个Set里面
    4. 可以用来进行一定程度的压缩(如果实际中我们需要辨识的类别小的话)
        * 其实实际场景比训练集更容易存在Class Imbalance这样的问题s
    5. 其实是可以联系Mutual Learning
    6. 原文中使用的数据集通过在重构第二个数据集的时候*对每一类都固定只选K个样本*构造一个不再Imbalance的数据集
        * 但是实际上的情况会更加Imbalance，是否是*等到满足条件才开始训练？*
* **效果和直接finetune好在哪里？**
    1. 解决直接训会崩的问题，有保底...


## Related Work（BackGround Knowledge）
* Transfer Learning
    * 一样是用来处理Not Enough Label的问题
    * Reuse The Net去解决一个不同领域的问题 
    * Example：*low-shot learning*
    * TL可以被认为是在Evaluate *Unsupervised Learning* 
* Semi-Supervised Learning
    * 使用额外的未标注数据来提升监督学习的效果(和本文的主旨非常一致)
* Distillation
    * 一开始是用来压缩的
    * 可以认为是一种Self-Training
* Data-Augmentation
    * *本文的做法也可以看做是一种特殊的Data Augumentation*