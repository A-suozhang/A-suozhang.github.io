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

#### AutoEncoder

Autoencoder本身只是一个encoder-decoder架构的神经网络，它与主成分分析PCA有着许多联系，如果activation function中不带有非线性性时候， autoencoder is reduced to PCA。其中encoder-decoder的中间hidden-state(图中的code，有时候也叫做bottleneck层)的size设置较为关键。过小会导致表征能力不足而难以恢复出原图（一个合理的hidden size表示了minimal information to encode the image）。autoencoder的训练过程可以看做是寻找一种方式，将原始图片**"compresse into hidden state"**，同时又能够**”保持足够多的信息(能够恢复出原始图片)“**。它可以无监督的进行训练(无需label，目标为拟合输入自己)。其问题定义较为简单，对于输入$$x$$，希望autoencoder输出$$x'$$拟合原本的输入：

$$L(x,x')=||x-x'||^2$$

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230331111731.png)

一个典型的autoencoder的应用就是去噪任务，对于一个加噪的输入，加一个噪声，希望恢复出原本的图片。引导训练不会overfit到noise之中。   

**那么autoencoder的问题是什么？**，autoencoder接受**离散的**各个数据点，而不能access到the whole continuous latent space。（类似于supervised learning setting中的样本不足的问题），因为在训练数据中没有见过，所以autoencoder难以保证能够泛化到latent space中的其他部分。autoencoder难以处理**连续的latent space**，它不能adapt to arbitrary input since it is not trained on。 为解决这些问题，**Variational AutoEncoder利用采样来填充离散的latent space**。

#### Variational AutoEncoder

> 主要参考并引用了[这篇博文](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)的介绍逻辑

与autoencoder只学习恢复回原始数据这样的**某一个数据点**不同，VAE在latent space进行采样，并学习的是一个**后验分布** $$p(z \mid x)$$（类似Bayesian Statistics中的思想

为理解VAE的推导过程，我们需要从两种视角看这个问题。首先，我们将从**概率模型**(Probabilistic Model)的角度分析该问题并给出ELBO(Evidence Lower Bound)的推导。然后，用**神经网络**的语言重新解释该过程，将两者对应。

---

当**从概率模型的视角**看这个过程，我们需要先放下关于Deep Leanring与NN的术语。在概率模型的视角下，一个VAE包含了概率模型，建模了数据$$x$$与隐变量$$z$$之间的关系(某个概率模型$$p(x,z)$$建模了数据和隐变量的联合分布joint distribution)。生成的步骤可以看做，对某一个数据点$$i$$,我们会从某个连续的隐空间分布$$p(z)$$中采样一个$$z_i$$，然后利用模型的likelyhood$$p(x \mid z)$$生成$$x_i$$。likelyhood可以由贝叶斯公式得到$$p(x \mid z)=p(x,z)/p(z)$$。从该分布中采样即可得到我们需要的$$x_i$$。

如何训练该概率模型，我们希望该模型能够对给定的观测数据$$x$$，给定隐变量的先验分布$$p(z)$$，让概率模型能较好的拟合后验分布$$p(z \mid x)$$.

$$p(z \mid x) = \frac{p(x \mid z)p(z)}{p(x)}$$

分母上的evidence（观测结果的分布）是intractable的，如果要得到它，我们需要对latent variable $$z$$，计算其边缘分布$$p(x)=\int p(x \mid z)p(z)dz$$,而遍历z需要指数时间的代价，所以我们需要去拟合该后验分布。当没有解析解法的时候，通常需要用数值方法进行近似。

Variational Inference的方法用a family of distribution去拟合某一个分布。$$q_{\lambda(z \mid x)}$$包含了variational parameter $$\lambda$$实际是在索引(indexing)分布簇。e.g., 当$$q$$是一个高斯分布簇的时候$$\lambda_{x_i} = (\mu_{x_i},\sigma^2_{x_i})$$,表示了均值与方差（*不去优化某个特定分布，而是优化其分布的代表参数*）。我们用KL散度来描述函数簇的拟合程度，我们需要找到能最小化KL散度的变分参数$$\lambda$$:

$$q^{*}_{\lambda}(z \mid x) = \underset{\lambda}{\operatorname{argmin}}KL(q_{\lambda}(z \mid x)||p(z \mid x)) = $$

$$E_q[\log q_{\lambda}(z \mid x)] - E_q[\log p(x,z)] + \log p(x)$$

上式中仍然包含intractable的Evidence $$p(x)$$。将上式进行变化:

$$\log p(x) = KL(q_{\lambda}(z \mid x)||p(z \mid x)) - (E_q[\log q_{\lambda}(z \mid x)] - E_q[\log p(x,z)])$$

由于KL散度非负，总大于0，所以p(x)的下界，也就是Evidence Lower Bound就是：$$-(E_q[\log q_{\lambda}(z \mid x)] - E_q[\log p(x,z)])$$，其与$\lambda$有关。上式写为：

$$ELBO(\lambda) = KL(q_{\lambda}(z \mid x)||p(z \mid x)) - \log p(x) $$

由于$$\log p(x)$$与$$\lambda$$无关，所以对于$$\lambda$$最小化KL散度的优化问题，可以等效为最大化ELBO的优化问题。而ELBO是可以被计算的。
由前式得到：

$$ELBO(\lambda) = E_q[\log p(x,z)] - E_q[\log q_{\lambda}(z \mid x)]$$

由于各个样本采样独立，每个样本的ELBO可以被decompose为：(用贝叶斯公式将左项拆分出一个$$1/p(z)$$并且到右边组成KL散度的形式)

$$ELBO_i(\lambda) = E_{q_{\lambda}(z \mid x_i)}[\log p(x_i \mid z)] - KL(q_{\lambda}(z \mid x_i)||p(z))$$

由于varaiational parameter是对每个数据点都shared的，所以可以利用SGD去优化$$\lambda$$。

In practice, 我们用encoder直接预测Normal Distribution的$$\mu,\sigma$$得到一个分布，再采样得到一个确定的隐变量$$z$$。有趣的是，该采样步骤并不可导，不能进行反向传播。因此，我们需要引入重参数化Reparametrization，来估计采样过程的梯度。（我们或许会把它放在一个单独的section里）。

---

当从神经网络的视角看，我们首先会用一个encoder $$q_{\theta}(z \mid x,\lambda)$$去拟合后验分布。encoder由参数$$\lambda$$和输入$$x$$，输出隐变量$$z$$。其次，用另外一个decoder网络去拟合似然$$p(x\mid z)$$,接受隐变量z，输出x。认为encoder和decoder分别被$$\theta,\phi$$所parameterize。重写ELBO可以得到，为了最大化ELBO，直接将其负值作为神经网络的loss。

$$ELBO_i(\theta,\phi) = E_{q_{\theta}(z \mid x_i)}[\log p_{\phi}(x_i \mid z)] - KL(q_{\theta}(z \mid x_i)||p(z)] $$

对此式的解释，第一项我们可以看做是希望最大化likelyhood函数$$\log_p(x \mid z)$$,作为"Reconstruction Loss"，希望尽可能的从$$z$$中能够复原出$$x$$的信息。第二项可以看做是一个regularization，我们希望去最小化这两个分布之间的距离，保证后验分布$$q(z \mid x)$$不偏离$$p(z)$$太远，描述”减少latent space compression“过程的信息损失。整个优化过程的物理意义较为清晰，这两者合起来就是为了保证approximated posterior $$q_{\lambda}(z \mid x)$$与真实的posterior $$p(z \mid x)$$之间的距离。

另外值得提到的一点是，”The variational inference“的过程本身，仅描述了用ELBO去优化variational parameter $$\lambda$$（encoder中改的参数）的过程。在NN训练过程中，我们的decoder参数也是通过优化一样的loss得到的，我们可以将其看做variationalEM(Expectation Maximization).

另外一个有趣且我没有完全理解的点是variational inference中的mean-field inference与amortized inference的对比，两者的主要区别在于是否**共享了参数** among data points。mean-field指的是将各个数据点分别独立处理，将分布factorized到每个样本的分布。$$q(z) = \Pi_i^N q(z_i;\lambda_i)$$,对于新样本，我们需要重新优化ELBO，并选择它所适合的参数。这样有更严格的更好的表征能力（无shared weight）。但是可以认为一定程度上limit模型的capacity和representation power，因为parameter被tied to samples.（看起来作者这样的写法区分了expressive与model representative power.）而amortized的意思为”平摊“，训练代价被平摊到了各个样本上，各个样本shared parameter。当应对新的数据的时候，可以选择微调，或者只是认为现有的参数能够泛化到它。



# References

- [Tutorial - What is a variational autoencoder?](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)
- [Comprehensive Introduction to Autoencoders](https://towardsdatascience.com/generating-images-with-autoencoders-77fd3a8dd368)
- [Yang Song - Generative Modeling by Estimating Gradients of the Data Distribution](https://yang-song.net/blog/2021/score/)
- [The illustrated stable diffusion](https://jalammar.github.io/illustrated-stable-diffusion/)
- [How diffusion works](https://theaisummer.com/diffusion-models/)


### 测试公式撰写

- 这种方式会生成一个渲染出的svg图片，但是撰写时比较麻烦，暂时弃用，均采用“$$$$"的方式撰写公式，用jekyll在localhost上直接监测公式。（在博客根目录执行`jekyll s`，在localhost的4000端口打开）
    - ![equation](https://latex.codecogs.com/svg.latex?P(A\|B)=\frac{P(B\|A)*P(A)}{P(B)})
- Edge有时候会出现MathJax的字体渲染错误,like [this post](https://stackoverflow.com/questions/64511072/mathjax-displays-math-formula-as-normal-text-in-microsoft-edge-browser),通过修改mathjax的默认渲染引擎（似乎只需要改一次）可以解决。
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230329120750.png)