---
layout:     post                    # 使用的布局（不需要改）
title:      从VAE到Diffusion # 标题 
subtitle:   personal notes about generative model #副标题
date:       2023-03-29            # 时间
author:     tianchen                      # 作者
header-img: img/diffusion/disco-25.png  #这篇文章标题背景图片  
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

由于KL散度非负，总大于0，所以p(x)的下界，也就是Evidence Lower Bound就是：$$-(E_q[\log q_{\lambda}(z \mid x)] - E_q[\log p(x,z)])$$，其与$$\lambda$$有关。上式写为：

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

### Diffusion



#### Diffusion is a type of VAE

再次简述一下vae，它建模的是一个含参概率分布

$$p_{\theta}(x) = \int_z p_{\theta}(x \mid z)p_{\theta}(z)$$

先验为$$p_{\theta}(z)$$服从某个分布，观测模型$$p_{\theta}(x \mid z)$$。我们假设$$p(z)$$服从高斯分布，其参数$$\mu_{\theta}(z)$$与$$\sigma_{\theta}(z)$$由一个神经网络预测。其优化目标为：

$$\theta^{*} = \underset{\theta}{\operatorname{argmax}}E_{x\sim \hat{p}(x), z \sim p(z)}[\log p_{\theta}(x\mid z; \theta)]]$$

由于这样需要遍历$$z$$的连续空间而导致每个特定数据点上的likelyhood无法保证（尤其对高维空间），因此，VAE采用采样方式，从一个参数化的分布$$q_{\theta}(z \mid x)$$中得到（VAE中的decoder部分），更倾向于focus on z that are more likely to generate x的部分。

$$ \log p(x) = \int_z p_{\theta}(x \mid z)p(z) $$

$$ \log p(x) = log E_{z \sim q_{\phi}(z \mid x)}[\frac{p_{\theta}(x\mid z)p(z)}{q_{\phi}(z \mid x)}] $$

由于对期望取log无意义，需要**将log放到期望E里面**。此时，利用Jenson不等式可以得到ELBO项加以优化。

$$\log p(x) \ge E_{z \sim q_{\phi}(z \mid x)}[\log \frac{p_{\theta}(x \mid z)p(z)}{q_{\phi}(z \mid x)}]$$

为了获得对于$$\phi$$的导数，需要引入重参数化技巧，由于采样步骤$$z\sim q_{\phi}(z \mid x)$$.

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230406192404.png)

当我们认为diffusion是一种特殊的Hierarchical VAE，**前向过程inference model没有可学习参数**，且最后目标为接近标准高斯。前向模型为：

$$q(x_t \mid x_{t-1}) = N(x_{T};x_{t-1}\sqrt{1-\beta_{t}},\beta_{t}I)$$

让最终的$$q(x_t \mid x_0) \approx N(0,1)$$。其中$$\beta$$是一个超参数，follow了一种固定的annealing方式，方差逐渐减小。当我们定义$$\alpha_t := 1-\beta_t$$, $$\bar{x_t} = \Pi^{t}_{s=1} \alpha_{s} $$。该流程的优秀特性在于，“跳步采样”（在DDIM的文章中证明了），可以随意采样时间步$$p_{]\theta}(x_{t-1} \mid x_t)$$，而不用进行整个生成流程。

$$q(x_t \mid x_0) = N(\sqrt{\bar{\alpha_t}}x_0, (1-\bar{\alpha_t})I)$$

将该过程的ELBO展开后可得到：

$$L = E_q[-\log p(x_T) - \log \frac{p_{\theta}(x_0 \mid x_1)}{q(x_1 \mid x_0)} - \sum_{t>1}^{T} \log \frac{p_{\theta}(x_{t-1} \mid x_t)}{q(x_t \mid x_{t-1})}]$$

最后一项可以展开为：

$$ \sum_{t>1}^{T} \log \frac{p_{\theta}(x_{t-1} \mid x_t)}{q(x_t \mid x_{t-1})} = \sum_{t>1}^T \log \frac{p_{\theta}(x_{t-1} \mid x_t)}{q(x_{t-1} \mid x_t,x_0)} + \log \frac{q(x_{t-1} \mid x_0)}{q(x_t \mid x_0)} $$

其中$$\log q(x_t \mid x_0) = log q(x_T \mid x_0) - q(x_1 \mid x_0)$$,改写后的loss为(将原本的第二项和第三项右侧的sum拆开后合并)

$$L := (-\log p(x_T) + \log q(x_T \mid x_0)) - \log p_{\theta}(x_0 \mid x_1) - \sum_{t>1}^{T} \log \frac{p_{\theta}(x_{t-1} \mid x_t)}{q(x_{t-1} \mid x_t,x_0)}$$

第一项无参数，衡量了前向结果与标准高斯的差距。中间为重建项（Reconstruction Error）。$$L_{t-1}$$标识了转移概率与高斯的差距（实质上是$$ \sum_{t=2}^T D_KL(q(x_{t-1} \mid x_t) \| p_{\theta}(x_{t-1} \mid x_t)) $$）。

以上过程是最早的Diffusion model的做法(*[1]:Deep Unsupervised Learning using Nonequilibrium Thermodynamics*)，与DDPM有所区别。DDPM(*[2]:Denoising Diffusion Probabilistic Models*)采取了不同的parameterization方式，简化了训练。Diffusion的前向过程的逆过程，如$$q(x_{t-1} \mid x_t)$$本身没有解析解，intractable。但是综合考虑初始态的$$q(x_{t-1} \mid x_t,x_0)$$可以通过Bayes Rule可以推导得到:

$$ q(x_{t-1} \mid x_t, x_0) = \frac{q(x_t,x_0,x_{t-1})}{q(x_t,x_0)} = \frac{q(x_0)q(x_{t-1} \mid x_0)q(x_t \mid x_0,x_{t-1  })}{q(x_0)q(x_t \mid x_0)}$$


$$ q(x_{t-1} \mid x_t, x_0) = q(x_t \mid x_{t-1},x_0)\frac{q(x_{t-1} \mid x_))}{q(x_t \mid x_0)} $$

由马尔可夫性，有 $$q(x_t\mid x_{t-1},x_0) = q(x_t \mid x_{t-1})$$,则可得

$$ q(x_{t-1} \mid x_t, x_0) = q(x_t \mid x_{t-1}) \frac{q(x_{t-1}\mid x_0)}{q(x_t \mid x_0)}$$

由于上式的三个部分都服从高斯分布，或是高斯分布的乘积（依旧是高斯分布），因此可以写作：

$$ q(x_{t-1} \mid x_t,x_0) = N(x_{t-1};\tilde{\mu}(x_t,x_0),\tilde{\beta_t}I) $$


$$ \tilde{\mu_t}(x_t,x_0) = \frac{\sqrt{\bar{\alpha_{t-1}}}\beta_t}{1-\bar{\alpha_t}}x_0 + \frac{\sqrt{\alpha_t}(1-\bar{\alpha_{t-1}})}{1-\bar{\alpha_{t}}}x_t$$

$$ \tilde{\beta_t} = \frac{1-\bar{\alpha_{t-1}}}{1-\bar{\alpha_t}}\beta_t $$

对应的，我们用一个含参数的NN来拟合与建模该过程

$$ p_{\theta}(x_{t-1} \mid x_t) = N(x_{t-1}; \mu_{\theta}(x_t,t),\sigma_t^2 I) $$

其中，方差$$\sigma_t^2$$是一个时变的常数(类似$$\beta_t$$),而均值$$\mu_{\theta}(x_t,t)$$由神经网络得到，将输入图像映射到该期望。该神经网络在各个timestep共享参数，时间步$$t$$以positional embedding的方式引入。

经过这样的处理与改写，损失中的第三项($$L_{t-1}$$)可以被改写为：

$$ L_{t-1} = E_{t,x_t,x_0}[\frac{1}{2\sigma^2_t} \| \tilde{\mu_t}(x_t,x_0) - \mu_{\theta}(x_t,t)  \| ] $$

原问题被看做了一个拟合两个高斯分布的均值的问题。但是，DDPM的作者further对这个形式进行了进一步的改写。

通过一个重参数化（这里引入重参数化并不是为了让不可导的采样过程变可导，而只是更换一种$$x_t$$的表达方式，将$$x_t$$与$$\mu$$相关，改为$$\epsilon$$来表示$$\mu$$，which is straightforward,去求加的噪声如何变化在去噪过程中比较合理）。$$x_t$$可以被改写为初值$$x_0$$加上了一系列随机采样得到的噪声$$\epsilon \in N(0,1)$$.

$$x_t(x_0, \epsilon) = \sqrt{\bar{\alpha_t}}x_0 + \sqrt{1-\bar{\alpha_t}}\epsilon $$

$$x_0 = \frac{x_t(x_0,\epsilon) - \sqrt{1-\bar{\alpha_t}}\epsilon}{\sqrt{\bar{\alpha_t}}}$$

将其代入$$q(x_{t-1},\tilde{\mu}(x_t,x_0),\beta_t I)$$之中，该期望可以被写作,代入化简（参考$$ \bar{\alpha_t} = \bar{\alpha_{t-1}}\alpha_t$$, $$ \beta_t = 1-\alpha_t $$）得到：

$$\tilde{\mu_t}(x_t(x_0,\epsilon),\frac{1}{\sqrt{\bar{\alpha_t}}}(x_t(x_0,\epsilon)-\sqrt{1-\bar{\alpha_t}}\epsilon))$$

$$ \tilde{\mu_t} = \frac{1}{\sqrt{\alpha_t}}x_t - \frac{\beta_t}{\sqrt{\alpha_t}\sqrt{1-\alpha_t}} $$

而我们要优化的目标可以被改写:

$$ L_{t-1} = E_{x_0,\epsilon,t}[ \frac{1}{2\sigma_t^2} \| \frac{1}{\sqrt{\alpha_t}} \cdot (x_t(x_0,\epsilon) - \frac{\beta_t}{\sqrt{1-\bar{\alpha_t}}}\epsilon) - \mu_{\theta}(x_t(x_0,\epsilon),t)  \| ]  $$


上式中，当NN $$\mu_{\theta}$$最接近$$\frac{1}{\sqrt{\alpha+t}}(x_t(x_0,\epsilon)-\frac{\beta_t}{\sqrt{1-\bar{\alpha_t}}})$$的时候，取得最优$$\theta$$。由于我们给NN的输入为$$\mu_{\theta}$$，所以NN实际上是在学习一个affine transform，包含了一些与t相关的常数与未知的噪声$$\epsilon$$。将$$\mu_{\theta}$$改写为$$\epsilon_{\theta}$$可以得到。

$$\mu_{\theta}(x_t,t) = \frac{1}{\sqrt{\alpha_t}}(x_t(x_0,\epsilon) - \frac{\beta_t}{\sqrt{1-\bar{\alpha_t}}}\epsilon_{\theta}(x_t(x_0,\epsilon),t))$$

从而有

$$ E_{x_0,\epsilon,t} [\frac{\beta_t^2}{2\sigma_t^2\alpha_t(1-\bar{\alpha_t})} \| \epsilon - \epsilon_{\theta}(x_t(x_0,\epsilon),t) \| ]  $$

这样将原本对齐两个分布的期望均值的任务，可以被改写为用一个NN预测每一步的噪声$$\epsilon$$的过程。

当然，“估计噪声”有一个**等效的做法**是“估计原始图片”，由于每个时间t都存在确定的,所以预测 $$\epsilon$$ ，和预测 $$x_0$$ 等效 (which is what is actually used). 

$$ x_t = x_0 + \epsilon $$

目标可以写成：

$$ E_{x_0,x_t} [\frac{\beta_t^2}{2\sigma_t^2\alpha_t(1-\bar{\alpha_t})} \| x - x_{\theta}(x(t),t) \| ]  $$

在实际的应用中，包含 $$\alpha, \beta$$的系数项，通常不采用exact形式，被写为 $$ \gamma_t $$（有时为 $$\frac{\alpha_t}{1-\alpha_t}$$ ,甚至有时为1）。

总结DDPM的过程，分为以下几步。首先从随机噪声中采样出$$x_0 \sim p(x)$$,并选取一个随机的时间步$$t$$与一些噪声$$\epsilon \sim N(0,1)$$。按照前向推理加噪，计算$$x_t(x_0,\epsilon)$$。代表了从$$q(x_t,x_0)$$。前向的Posterior $$q(x_{t-1} \mid x_t,x_0)$$的解析解。我们希望一个含参数的模型$$p_{\theta}(x_{t-1} \mid x_t)$$能够拟合前向的posterior，减少KL散度（经过上述证明只要让NN去拟合噪声$$\epsilon$$即可。）

总结来看，DDPM与VAE相同的地方主要在于算法范式，它可以被看做是一种特殊的Hierarchical VAE。前向过程不含参，且为标准高斯的加噪声过程。其优化目标(Obnjective)是类似ELBO的优化lower bound的方式。而也具有如下区别：如DDPM的前向过程destroy了输入的信息，将其退化为了标准高斯，而VAE需要的是获得输入信息的compressed的表征。DDPM的”隐空间“的尺度与原始数据一致，而VAE可以自主选择与设计隐空间。在DDPM中，不同timestep的generative model实际上是共享了同一个NN模型(U-Net)。

#### DDIM

> 该部分的笔记参考了[LilianWeng's blog: diffusion models](https://lilianweng.github.io/posts/2021-07-11-diffusion-models/)

#### Score-based SDE formulation

> in "Score-Based Generative Modeling through Stochastic Differential Equations"
> Ref [Song Yang's Blog](https://yang-song.net/blog/2021/score/)

--- 

**Score-Matching的问题formulation:**它所对比的是一般的likehood-based method,直接去建模probability distribution本身（一般用PDF来建模），引入可训练参数并拟合：$$Z_{\theta}>0$$是一个常数，与$$\theta$$有关，保证$$\int p_{\theta}(x)dx=1 $$,而$$f_{\theta}$$通常被称作”unnormalized probability model/energy-model“

$$ p_{\theta}(x) = \frac{e^{-f_{\theta}(x)}}{Z_{\theta}} $$

优化目标为最大似然：

$$ \max \sum_{t=1}^N \log p_{\theta}(x_i) $$

由于该式需要计算遍历无限大的$$Z_{\theta}$$（对于一个general的$$f_{\theta}(x)$$），所以现有的方法通过限制模型架构的方式(autogressive中用casual convolution，invertible network for Normalizing flow)，或者去拟合$$Z_{\theta}$$(Variational Inference, MCMC sampling),问题在于**computationally expensive**. 

通过将优化目标改为Score function(PDF的梯度) $$\nabla_x \log p(x)$$,可以省去这个intractable的normalization constant ($$\nabla_x \log Z_{\theta} = 0$$)。一般用一个Score-based model $$s_{\theta}(x)$$ 去拟合Score。类比于likelyhood-based model,优化目标为最小化**Fisher Divergence**(intuitive解释为：gt score与score-based model输出的square-L2距离)

$$E_{p(x)}[||\nabla_x \log p(x) - s_{\theta}(x)||^2]$$

有一系列”Score Matching“方法能够minimize fisher divergence, 且不需要gt data score的信息。可以直接在某个数据集上用SGD更新（类比似然模型的情况）。此外，Score matching的objective本身并不对$$s_{\theta}(x)$$加以限制，提供了模型的灵活性。

完成Score-macthing的训练之后，可以利用Langevin Dynamics（郎之万动力学）从中采样（因为我们并不知道概率分布$$p(x)$$，需要从$$s(x)$$中采样。它提供了一个MCMC采样，只需要知道一个分布$$p(x)$$的score function。它初始化采样链，从一个任意的prior分布 $$x_0 \sim \pi (x)$$，然后进行如下的扩散过程：

$$ x_{i+1} <- x_i + \epsilon \nabla_x \log p(x) + \sqrt{2\epsilon} z_i, i=0,1,..K $$

其中，$$z_i \sim N(0,I)$$,当K趋向无穷大，$$\epsilon$$趋向无穷小的时候，误差为0。
感性理解，从一个先验分布，沿着目标分布的score(概率分布的梯度方向)加上了一个噪声进行移动，逐渐收敛到向分布$$p(x)$$采样。
 
---

**SMLD (Score Matching with Langevin Dynamics):** 这篇是Song Yang之前的工作，从Score-matching角度做生成，训练了一个Noise Condition Score Network(NCSN) model去拟合score。相比于最朴素的用score-matching直接做生成，由于训练集合数据难以fit整个数据流形（不严谨的表述），概率空间存在很多low-density的区域，在这些区域难以准确的estimate score dunction。解决方案为perturb data points with noise，并采用multiple noise scale。

对于这些加噪声的分布,可以在Langevin dynamics中逐渐减少Noise以完成逐步去除perturb，叫做annealed Langevin Dynamics过程。

结果不如DDPM一定程度上原因是因为仅用了朴素的CNN，而不是DDPM所采用的U-Net（本文中通过实验结果验证了搭配U-Net,SMLD也可以取得和DDPM相似的性能）。该方法与DDPM一并作为SDE Formulation的生成模型的两种特殊情况。 

---

**SDE formulation**利用SDE作为描述逐渐增长的noise-level扩散的（as a continuous-time stochastic process），前向过程可以写为： 

$$ dx = f(x,t)dt+g(t)dw $$

$$f(\cdot,t)$$ 是一个vector function,一般叫做drift coefficient, $$g(t) \sim R$$ diffusion coefficient. w 是一个标准的布朗运动（维纳过程），dw可以看做是白噪声。该随机微分方程的解是一个continuous collection of random variables $$ {x(t)} t \in [0,T] $$。用$$p_t(x)$$表示第t时刻的边缘分布（$$x(t)$$，其实是$$x(t1,t2...tn)$$的联合分布）。$$p_0(x)$$是不带噪声的clean分布，当足够多的加噪步骤后，$$p_T(x)$$变成了纯粹的噪声，叫做prior distribution. 

注意该SDE其实是handcrafted的(代表了加噪声的方式)，作者展示了3种典型的SDE：the Variance Exploding SDE (VE SDE), the Variance Preserving SDE (VP SDE), and the sub-VP SDE。这些SDE都有一个对应的具有相同边缘分布的反向SDE。反向去除noise pertubation过程可以通过annealed Langevin dynamics得到。

$$dx = [f(x,t) - g^2(t) \nabla_x \log p_t(x)]dt + g(t) dw$$

该反向SDE中，有Score是需要用神经网络$$s_{\theta}(x,t)$$进行拟合。在训练中，其优化目标为：

$$ argmin E_t \{ \lambda(t) E_{x(0)}E_{x(t)} [ ||s_{\theta}(x(t),t) - \nabla_{x{t}} \log p_{0t}(x(t) \mid x(0)) ||^2 ] \} $$

该公式描述了采样出某个$$x(0)$$与某个时间t的$$x(t)$$并进行训练的过程。其中$$\lambda(t)$$是一个时变的weighting value（通常让它能balance the magnitude of score）。可证明它和优化下式等效：

$$ argmin E_t \{ \lambda(t) E_{x(t)} [ ||s_{\theta}(x(t),t) - \nabla_{x{t}} \log p_{0t}(x(t)） ||^2 ] \} $$

此外，可以证明，当$$f(\cdot,t)$$是affine的情况下，transition kernel $$p(x_t \mid x_0)$$是高斯的。在这种情况下，Score function有解析解 $$ \nabla_x =  - \frac{x s- \mu}{\sigma^2} =  - \frac{\epsilon}{\sigma} $$, score matching等效为预估噪声,也等效于预估还原原始图片$$x_0$$。

证明过程可以感性理解为，当f(t)关于x为affine时，前向过程可以改写为: 

$$dx = f(t)x(t)dt + g(t)dw$$

由于w是一个维纳过程，它的增量过程dw服从高斯分布，当t确定的时候，左侧项是一个无穷小值(乘上了dt)，dx代表了随机变量x的增量。


#### 对High-Freq Redundancy的分析

> in "Improving Diffusion Model Efficiency Through Patching"

- denoise过程的感性图像化interpretation

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230620171244.png)

# References

- [1] Deep Unsupervised Learning using Nonequilibrium Thermodynamics. Jascha Sohl-Dickstein, Eric A. Weiss, Niru Maheswaranathan, Surya Ganguli. 2015
- [2] Denoising Diffusion Probabilistic Models Jonathan Ho, Ajay Jain, Pieter Abbeel. 20202. 
- [3] Denoising Diffusion Implicit Models. Song Jiaming, 2022

- [Tutorial - What is a variational autoencoder?](https://jaan.io/what-is-variational-autoencoder-vae-tutorial/)
- [Comprehensive Introduction to Autoencoders](https://towardsdatascience.com/generating-images-with-autoencoders-77fd3a8dd368)
- [Yang Song - Generative Modeling by Estimating Gradients of the Data Distribution](https://yang-song.net/blog/2021/score/)
- [The illustrated stable diffusion](https://jalammar.github.io/illustrated-stable-diffusion/)
- [How diffusion works](https://theaisummer.com/diffusion-models/)
- [diffusion probabilistic model is a kind of VAE](https://angusturner.github.io/generative_models/2021/06/29/diffusion-probabilistic-models-I.html)
- [Diffusion Models](https://lilianweng.github.io/posts/2021-07-11-diffusion-models/)

### 测试公式撰写

- 这种方式会生成一个渲染出的svg图片，但是撰写时比较麻烦，暂时弃用，均采用“$$$$"的方式撰写公式，用jekyll在localhost上直接监测公式。（在博客根目录执行`jekyll s`，在localhost的4000端口打开）
    - ![equation](https://latex.codecogs.com/svg.latex?P(A\|B)=\frac{P(B\|A)*P(A)}{P(B)})
- Edge有时候会出现MathJax的字体渲染错误,like [this post](https://stackoverflow.com/questions/64511072/mathjax-displays-math-formula-as-normal-text-in-microsoft-edge-browser),通过修改mathjax的默认渲染引擎（似乎只需要改一次）可以解决。
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20230329120750.png)