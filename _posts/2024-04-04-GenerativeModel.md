---
layout:     post                    # 使用的布局（不需要改）
title:      Generative Model vs. Discriminative Model
subtitle:   The New Era.
date:       2024-04-04          # 时间
author:     tianchen                      # 作者
header-img: img/diffusion/disco-31.png  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 心得
---

> 在整理前沿课的Talk编背景的时候，想到了这个问题“针对generative model的compression，和之前时代是否有什么新问题？”，于是找了一些材料。

# Q: Generative Model vs. Discriminative Model?

我们将学习的任务简单抽象为，给定一组数据点$${x^{(1)},...,x^{(m)}}$$,与其对应的输出$${y^{(1)},...,y^{(m)}}$$，想要学习得到一个模型，给定x去预测y。

引用[课程cheatsheat](https://stanford.edu/~shervine/teaching/cs-229/cheatsheet-supervised-learning)中的对比table（被不少其他课程所引用了），两者的典型对比如下：

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20240404155833.png)

可以看到两者比较关键的区别在于**“学习的目标不同”**，判别式模型（Discriminative Model）直接去学习 $$P(y \mid x)$$(直接学习从输入x的空间到y空间，通常也就是类别标签的空间{0,1}), 而生成式模型（Generative Models）学习似然函数 $$P(x \mid y)$$ ，并建模$$p(y)$$（作为prior），再基于观察到的x（p(x)为evidence），通过贝叶斯公式导出后验概率：

$$P(y \mid x) = \frac{P(x \mid y) p(y)}{p(x)}. $$

当我们去做离散的prediction的时候，我们只需要 $$\operatorname{argmax}_y p(y \mid x)$$，不用考虑分母上的Marginal/Evidence项：p(x).

直观来思考，判别式模型仅学习了一个“Decision Boundary”的分布 $$P(y \mid x)$$,而生成式模型学习/建模了数据的分布$$P(y), P(x \mid y)$$, 并依据实际观察到的信息，基于贝叶斯概率公式来推导出后验分布： $$ P(y \mid x) $$。学习到了完整的概率分布。在下面的具体例子的对比分析中，我们将进一步讨论两者的关联与区别。

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20240404162717.png)

另外的，输出空间是离散（分类）或者是连续（回归），仅代表了“type of prediction”，不失以上判别与生成分类的泛化性（判别式模型也完全可以映射连续的输出空间，如Linear Regression）。

# Examples: Gaussian Discriminant Analysis vs. Logistic Regression

一个生成式模型完成判别任务的典型例子是Gaussian Discriminant Analysis (GDA)，我们假设似然函数去拟合的Likelyhood: $$p(x \mid y)$$是一个多元高斯 multivariant gaussian，可以被参数化为 mean: $$\mu$$, 和Covariance Matrix: $$\Sigma$$。

对于一个简单的2分类任务（因此输出y为一个Categorical Distribution），输入x是一个连续的随机变量，我们做出以下假设：

$$y \sim \operatorname{Bernoulli}(\phi)$$

$$x \mid y=0 \sim  \mathcal{N}(\mu_0, \Sigma) $$

$$x \mid y=1 \sim  \mathcal{N}(\mu_1, \Sigma) $$

模型中的参数有$$\phi, \Sigma, \mu_0, \mu_1$$,通过计算最大化log-likelyhood时候的值，可视化为图可以看到两个高斯分布fit了两簇数据。中间的横线给出了decision boundary：$$p(y=1 \mid x)$$。

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20240404164420.png)

---

考虑Logistic Regression（判别式的分类模型）,我们将似然函数看做y=1个数关于x的函数： $$p(y=1 \mid x) = \frac{1}{1+exp^{(-\theta^T x)}}$$, 这里的$$\theta$$可以看做是函数，拟合了$$\phi, \Sigma, \mu_0, \mu_1$$。最终得到了这样一个确定性的modelling $$p(y=1 \mid x)$$.

**他们之间的关系？**: 当$$p(x mid y)$$是多元高斯的时候，$$p(y \mid x)$$一定follow logistic的形式，但是反之不一定。因此，GDA(generative)相比于Logistic Regression(discrimnative)给出了更强的assumption。所以**当先验假设满足的时候，GDA将比LR获得更好的拟合**,GDA也更加的Data-Efficient（如果抛开数据量少了，数据分布违背先验高斯分布的情况；由于实际情况下大多是数据量充足，且并不高斯，所以LR用的比GDA多）。同时，**由于假设更弱，LR具有更好的robust，且less sensitive to错误的assumption**，LR只假设了后验分布是Bernoulli分布 $$P(y \mid x; \theta) \sim \operatorname{Bernoulli}(\phi)$$，其参数估计为logistic形式 $$\phi = P(y=1 \mid x; \theta) = \frac{1}{1+exp(-\theta^T x)}$$ 。比如如果$$P(x \mid y=0)$$是Poisson的，仍然满足logistic。


---

## Extras: A few about bayesian

> 查询相关资料的时候，看到了几个不错的Bayesian入门博客，随手摘录一些

首先是这个[漫画系列](https://www.smbc-comics.com/)，幽默

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20240404171909.png)

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20240404172046.png)

- 引入**共轭分布(conjugate distribution)**概念的原因：先验与后验分布来自于同一个分布族，可以简化计算


# References

- [CS229 Machine Learning Cheetsheet](https://stanford.edu/~shervine/teaching/cs-229/cheatsheet-supervised-learning)
- [CS229 Lecture Notes, Andrew Ng](https://see.stanford.edu/materials/aimlcs229/cs229-notes2.pdf)
- [Blog: Decoding Generative and Discriminative Models](https://www.analyticsvidhya.com/blog/2021/07/deep-understanding-of-discriminative-and-generative-models-in-machine-learning/)

- [走进贝叶斯统计（一）—— 先验分布与后验分布 - KERO的文章 - 知乎](https://zhuanlan.zhihu.com/p/401258319)