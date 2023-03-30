---
layout:     post                    # 使用的布局（不需要改）
title:      从VAE到Diffusion # 标题 
subtitle:   personal notes about generative model #副标题
date:       2023-03-29            # 时间
author:     tianchen                      # 作者
header-img:  img/2022_0710/5.png   #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - survey
---

> 记录梳理一下自己学习了解generative model的一系列笔记，合并在这里，for further reference

# Preliminary

> 首先回顾一些基础知识

#### 频率与贝叶斯视角下的NN学习

> 以下内容为[花书](https://github.com/MingchaoZhu/DeepLearning)的5.7节的拓展笔记

让我们首先当一个**频率派**，从估计(Estimation)的视角进行切入，我们从某个未知的真实数据分布$$p_{data}(x)$$中独立的采样得到一系列样本$$X=\{x^{(1),...,x^{(m)}}\}$$。我们定义一个似然函数$$p_{model}(x;\theta)$$将任意输入$$x$$映射为真实的概率$$p_{data}(x)$$，从**函数视角**看，通过最大化该似然函数的值，我们以更大概率准确估计了原始分布。由于每个样本的采样独立，可以改写为sum的形式:

$$\theta_{ML}= \underset{\theta}{\operatorname{argmax}}{\sum_{i=1}^{m}log p_{model}(x^{(i)};\theta)}$$

不影响优化目标，我们可以对上式除以采样参数个数$$m$$，转化为期望的形式：

$$\theta_{ML} = \underset{\theta}{\operatorname{argmax}}E_{x\sim\hat{p}_{data}} \log p_{model}(x;\theta) $$

而从**概率分布**的视角看，它描述了一个含参数$$\theta$$的，与$$x$$相同空间概率分布，我们需要减小该模型分布$$p_{model}(x;\theta)$$与训练集上的经验分布$$\hat{p}_{data}(x)$$，两者之间的距离。两个概率分布之间的距离可以由KL散度来衡量。

$$D_{KL}(\hat{p}_{data}(x)||p_{model}(x)) = E_{x \sim \hat{p}_{data}} [ log \hat{p}_{data}(x) - log p_{model}(x) ] $$

其中左侧第一项仅与从数据集中采样产生数据有关，与待优化参数$$\theta$$无关，因此可以删除，最小化目标为：

$$-E_{x\sim\hat{p}_{data}}[log p_{model}(x)]$$

与最大似然估计的目标相一致。此外，由于交叉熵为KL散度加上香农熵，而$$\hat{p}_{data}$$的香农熵是一个常数，因此优化KL散度与交叉熵等效。

$$H(P,Q)=H(P)+D_{KL}(P||Q)$$

在我们更常见的监督学习范式下，做最大似然估计(MLE)通常以条件概率的形式出现，which只需要将上式中的$$p_{data}(x)$$替换为$$p(Y \mid X)$$，并考虑采样出的数据对$${x^{(i)},y^{(i)}}$$。

$$\theta_{ML} = \underset{\theta}{\operatorname{argmax}}\sum_{i=0}^{m} \log p(y^{(i)} \mid x^{(i)};\theta)$$

---

当然，仅仅做一个频率派，不考虑先验知识的影响，是前康德的(笑)，确切来说，是前贝叶斯的。不将真实参数$$\theta$$看做一个未知的确定值，而将其看做是随机的(随机变量)。我们将数据集中的数据看做确定的观测(Evidence)，而不是如前者将它看做一个未知的分布。

对应贝叶斯公式，其中$$P(B)$$为Evidence（观测到的结果，数据集中的样本），$$P(A)$$为Prior（先验知识，假设参数$$\theta$$满足某分布,该分布一般是一个比较高熵，或者说高度不确定的分布，比如均匀或者高斯,之后再经过观测数据矫正为确定的分布）。概率模型，或者说似然函数$$P_{\theta}(B \mid A)$$,将A映射到观测B。或者说，描述了，将原本服从某先验分布的随机变量A，转化为符合观测B的新分布。$$P(A \mid B)$$则为Posterior后验概率，描述了给定观测时，待确定参数的条件分布。

$$P(A|B)=\frac{P(B\mid A) \cdot P(A)}{P(B)}$$

或者用实际情况的写法: 

$$p(\theta,x^{(1)},...,x^{(m)}) = \frac{p(x^{(1)},...,x^{(i)}\mid \theta)p(\theta)}{p(x^{(1)},...,x^{m})}$$

经过实际观测数据$$x_{(i)}$$的矫正,利用贝叶斯准则结合了数据似然函数与先验,得到后验概率，建模了给定观测的待确定参数的概率分布。

由于贝叶斯估计将待优化参数$$\theta$$作为一个整体的随机分布，因此在预测/优化时，不像MLE仅用单点的$$\theta$$值，而是采用了全分布。每一个新加入的样本都会改变对$$\theta$$分布的预测。如当$$x^{m+1}$$被加入时，对其后验概率的预测为：

$$p(x^{(m+1)}|x^{(1)}...,x^{(m)}) = \int p(x^{(m+1)}|\theta)p(\theta|x^{(1)},...,x^{(m)})d\theta$$

经过积分过程，可以看做贝叶斯方法将数据集合的所有信息归纳到了某一个单独的点做估计，而不是独立的对每个采样点进行估计。贝叶斯估计的另一个关键特征是**“先验”**的引入。能够影响PDF朝更接近偏好先验的区域偏移（常用先验为偏好更光滑/简单的模型）。在训练数据有限的时候，贝叶斯学习往往泛化更好，但在大规模数据时会有较大的计算代价。

在实际情况中，使用完整的贝叶斯后验分布进行预测困难（积分），最大后验估计（Maximum A Posterior）提供了一个好的点近似。MAP选择后验概率最大的点（PDF曲线上的最高点，峰值）,公式如下（可以由贝叶斯公式推理得到，$$\log p(x)$$与$$\theta$$无关）

$$\theta_{MAP} = \underset{\theta}{\operatorname{argmax}}p(\theta \mid x) = \underset{\theta}{\operatorname{argmax}} \log p(x \mid \theta) + \log p(\theta)$$

上式中左侧项表示标准的对数似然项($$\log p(x \mid \theta)$$ )，右侧表示了先验分布($$ \log p(\theta) $$)。

有趣的是，如果先验是一个高斯分布$$\mathcal{N}(w,0,1/\lambda)$$,那么MAP中的对数先验项将正比于权重衰减的惩罚项$$\lambda w^T w$$。也就是说，高斯先验权重的MAP贝叶斯推断对应着权重衰减(或者是L2 Norm,两者在使用SGD时等效，用backprop的公式可以简单推导得到，参考[随手找的这篇文章](https://towardsdatascience.com/weight-decay-l2-regularization-90a9e17713cd))。该方法（先验的附加信息）可以减少最大后验点的方差，代价是增加了偏差。很多正则化的估计方法，如加weight decay的MLE，可以被解释为贝叶斯推断MAP的近似，很多其他正则化方法也同理。

推广来看，监督学习的问题可以被定义为：估计一个条件概率$$p(y \mid x)$$，我们可以用带正则项的最大似然估计，找到对于某个含参分布簇。

$$p(y\mid x; \theta) = \mathcal{N}(y, \theta^T x, I)$$

该回归任务可以被推广到分类任务，对于一个最简单的二分类问题($$\sigma$$为sigmoid函数，保证输出值在$$[0,1]$$之间)，有:

$$p(y=1\mid x;\theta) = \sigma(\theta^T x)$$














# References

- [Tutorial - What is a variational autoencoder?](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)


### 测试公式撰写

- 这种方式会生成一个渲染出的svg图片，但是撰写时比较麻烦，暂时弃用，均采用“$$$$"的方式撰写公式，用jekyll在localhost上直接监测公式。（在博客根目录执行`jekyll s`，在localhost的4000端口打开）
    - ![equation](https://latex.codecogs.com/svg.latex?P(A\|B)=\frac{P(B\|A)*P(A)}{P(B)})
- Edge有时候会出现MathJax的字体渲染错误,like [this post](https://stackoverflow.com/questions/64511072/mathjax-displays-math-formula-as-normal-text-in-microsoft-edge-browser),通过修改mathjax的默认渲染引擎（似乎只需要改一次）可以解决。
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230329120750.png)