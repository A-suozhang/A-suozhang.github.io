---
layout:     post                    # 使用的布局（不需要改）
title:      An Intro to RL              # 标题 
subtitle:   Think Like An Agent    #副标题
date:       2019-09-26                # 时间
author:     tianchen                      # 作者
header-img:  img/bg-street.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - DL
    - 综述
---
# Intro To RL
* 之前对RL的理解一直是只言片语，没有“系统地入个门”（雾），之后把RL相关的一些碎片知识都整理到这个post里面把

## 核心四元结构
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190908082919.png)
* 对Agent（智能体）从环境中学习的过程建模    

## 分类辨析 A Simple Taxonomy
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190908083740.png)
* Model Free & Model Based 理不理解所处的环境(或者说是agent有没有access to)
    * 不去对环境建模，直接接受真实世界的反馈 - **Model-Free**
        * *Q-Learning,Sarsa,Policy Gradients*
    * 智能体自己建立一个模型去模拟真实世界对自己行为的反馈 **Model-Based**
        * 相比于Model-Free多了一个建模的工具，多了一个沙盒模拟的过程
        * 相比Model-Free的方法，多了“想象力”和“推测”，Model-Free只能等待真实世界的反馈，一步步进行；而Model-Based的方法可以预测出未来几步，并且选择一个更加pleasant的预测来执行（思考一下围棋，看几步）
    * Model-Based**优势**为给与了agent根据environment而具有**Think Ahea**的能力，🤔(感觉体量都很大) -Example*AlphaZero*
    * Model-Based**劣势**在于这个model很难训练，需要完全通过experience来推测environment
    * 事实证明在大多数情况下model-free的方法都可以与model-based对等的效果，所以更为流行 
* **Policy-Based & Value-Based** （What To Learn）
    * Policy-Based 基于概率的方法：通过分析所处的环境，对所有action（决策）给一个发生概率
        * **直接对Policy进行参数化描述(是一种特殊的抽象)直接去拟合优化他**
    * 基本都是On-Policy（只基于most recent policy）
        * Policy Gradient
        * A2C、A3C
        * PPO
        * *Learn An Approximator Of $$ V^\pi(s) $$*
    * Value-Based 基于价值的方法，输出所有action的价值然后选择一个最好的    
        * Q-Learning,Sarsa
        * *Learn An Approxiamtion Of $$ Q_\theta(s,a) $$*
        * 基本是Off-Policy的(每一次Update可以用到所有data)
    * 两者结合
        * Actor-Critic: Actor基于Policy，基于概率给出动作，然后Critic对所作出的动作给出价值（相比于单纯的Policy Gradient加速了学习过程）
        * DDPG  
    * ❓: 有点像Hard/SoftMax?
    * *两者辨析*
        1. Policy Bssed更有理论基础，因为直接优化的是我们需要的东西，而ValueBased由Value这一间接优化，所以可能会训练出来坏的模型
        2. 两者在很多时候其实是等价的😲在两者之间的一些算法可以比较好的Trade Off这两种思想
* 回合更新/单步更新
    * 传统的比如Monte-Carlo Leaning，Policy Gradient（OLD），一整个回合结束才会总结更新
    * Q-Learing，Sarsa，Policy Gradient（NEW），每一步都更新，*效率更高*，现在大部分的都是
* On-Policy/Off-Policy
    * 在线： Sarsa
    * 离线： QLearning


            

## 关键定义
* RL的**核心**：
    1. 如何定义采取动作之后的Reward的评价标准（reward的设计不仅要符合最后我们所需要达到的目的，还需要注意在学习过程中不同action的比较）
        * 本质是需要让agent学到最好的状态转移矩阵
    2. 我们如何更新操作
    > Q-Learning中，会有一张很大的Table，记录了所有state对应的所有action的值，我们按样本学习每个单元格，使最后的Reward最大
    * 基于Value的方法 - DQN
        * 但是由于表过度大，DQN用一个NN来查表
        * DQN通过experience reply(解决机器学习独立同分布的问题) ❔
        * 用一个网络估计Q和现实Q之差，作为loss
    * 基于Policy的方法
        * 直接优化Policy简单明快（NN直接输出Policy），问题被转化为如何更新NN参数
            * 有stochastic / deterministic
                * Stochastic 需要采样，计算大  - PPO
                * Deterministic 已经证明过了策略梯度公式 - DPG/DDPG
    * 融合Value与Policy - Actor-critic方法 -> A3C
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190915141712.png)
* State：对于现实世界情况的完全描述（往往是比较抽象的建模）
    * 用一个Tensor来作为*Representation*
* Observation与State相对应，是当前世界不完全的描述，但是一般会比较具体  ~~我不到啊我瞎猜的~~
* Action State-决策空间
    * 可能连续（机器人在实际场景中）可能离散AlphaGO
* Policy - a rule used by agent to decide which action to take ~~我好像想不到一个太好的中文翻译，决策方式？~~
    * 物理意义是*状态动作价值函数Q(State,Action)取最大值时候的Action*
    * 可以是Deterministic（Hard） *Max*
    * 也可以是Stochastic （Soft） *Soft*
        * Categorical Policies - Used In Discrete
            * 就类似建立一个对离散Action的Classifier
            * 🤔已知了各个Action的概率分布，对输出需要做一个*Sampling*
        * Diagonal Gaussion Policies - Used In Continuous
            * 一个高维的高斯分布，可以由一个均值Vector&协方差矩阵来描述
                * Diagnol Gaussian Distribution是协方差矩阵是对角阵的时候,这个时候协方差矩阵也可以拿一个Vector来表示
            * 一般这种方式通过一个NN把输入的Observation映射为Mean Vector以及Covariance Matrix的一个代表，可以是
                * 直接是Vector Of Log Standard Variation （标准差的Log的向量）
                * 通过一个NN建立一个state到log(σ)的映射（或许会与输出mean的网络share param）
                * *用Log的原因是值域覆盖整个实数域，而标准差在(0,1),而且没有什么精度损失*
            * Sampling就是一个采样，已知一个分布之后 Mean + Variation*Noise
* Trajectory (episodes / rollouts)
    * A Sequence T={s0,a0,s1,a1...}
    * The 1st State Is ramdom sample of state-state-distribution
    * 状态之间的转换是由Environment所决定的
    * 为了算法研究，一般具有马尔科夫性，即只依赖于前一状态
* **Reward**  
    * 有时候也叫做*基于策略的状态价值函数 V(s)=E[G_t|S_t=s]*
    * rt = R{st,at,st+1}
    * 经常被简化为R(st)或者是R(st,at)
    * The Goal Of a Agent is to get *Maxium cumulative reward*
    * Final Reward(return) 
        * Infinite with discount R = sum((discount_factor)*rt)
            * discount factor (0,1) The Earlier Than Now,The Less
            * *Used For Better Convergence*
        * Finite Without Discount

* **The Main Model(Optimization View)**

$$ P(\tau|\pi) = \rho_0(s_0)\prod_{t=0}^{T-1}{P(s_{t+1}|s_t,a_t)}\pi(a_t|s_t) $$

* 该Ｐ表示了采用该Trajectory发生的概率(Under The Case Of A Certain Policy pi)

$$ J(\pi) = \int_{\tau}{P(\tau|\pi)R(\tau)} = E_{\tau \sim \pi}[R(\tau)]$$

* J表示了Return,是Reward函数的一个期望
* 而RL最核心的问题是

$$ \pi^* = \argmax_\pi{J(\pi)}$$

* **Value Function**
* ~~权当锻炼一下手打公式的能力~~
* 意义是Reward函数在某种情况下的期望，一般会用到以下几种
1. On-Policy Value Function: Given An Expecated *Policy* and Initial *State*

$$ V^(\pi)(s) = E_{\tau \sim \pi}[R(\tau)|s_0=s]$$

* Its Maximum(Optimal)

$$ V^*(s) = \max_{\pi}{E_{\tau \sim \pi}[R(\tau)|s_0=s]} $$

2. On Policy Action-Value Function: Take An Arbitary Action (which may not from policy)

$$ Q^\pi(s,a) = E_{\tau\sim\pi}[R(\tau)|s_0=s,a_0=a] $$

* Its Optimal

$$ Q^*(s,a) = \max_\pi{E_{\tau\sim\pi}[R(\tau)|s=s_0,a=a_0]} $$

* Their Connection

$$ V^*(\pi) = \max_{a}{Q^*(s,a)}$$

* **The Bellman Function**
* 与*动态规划*紧密联系,Bellman Equation是一个递归的形式。
    * 动态规划的本质： *将问题的求解转化为求解子问题来不断解决*而Markov性与动态规划对应
    * 可以认为当前的Q(值)函数存储了上一步的决策的信息，因而处理下一步(下一个子问题)的时候不用重新计算，而是可以直接取上一次的值来使用
* 🤔The Core Is *The Value Of Your Starting Point Is The Reward You Expect To Get Being There + Wherever You Land Next*
  * The [Bellman Equation](https://en.wikipedia.org/wiki/Bellman_equation) is a concept used In Dynamic Programming


* **The Main Model(Markov Model View)**
* Could be described as a MDP(Markov Decision Process) <S,A,R,P,$$ \rho_0 $$>
  * S - All States
  * A - All Valid Actions
  * R - reward Function $$r =  R(s_t,a_t,s_{t+1})$$
  * P - Transition Probaliliy Function
  * $$ \rho_0 $$ - The Starting State
* **MDP(Markov Decision Process)**
    * Markov决策过程,比一半的Markov过程,在每个状态转移的过程中,定义一个回报函数(一般在每一步上加一个折扣因子)
    * 决策优化的目标是寻找到一条 si->sj->sk 这样的**路径**,让回报函数最大化
    
## Mc & TD
* 是DL之外的另外分支,面向某些特别的Scenario 
* Mc: **Monte-Carlo**适用于"情节型任务"(与之对应的是连续型)
* TD: **Time Difference**结合了MC和DP(Dynamic Programming)
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190915141540.png)

## Q-Learning
* 基于表格的方法（表格的XY轴分别是当前state和当前时刻所作出的决策；里面的值是潜在奖励-Q值）
    * 这个表格*Q-table*就像是个状态转移矩阵：决定了每一个状态需要采取什么样的策略
* 那么Q值是怎么定义和更新呢？
    * 如果出现了新的state（插入全0或者特定初始化）拓展QTable
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190908104247.png)
* 每次更新都会用到Q的真实值和预测值
* alpha是学习率，表示学习速度 / gamma是对未来reward的衰减值（深谋远虑的程度）
* Q-Learning是Off-Policy的（Q-Table存储了之前的经验，其更新可以不基于正在经历的经验）
    * 但是我们并没有利用到Q-Learning OFF-policy的特性，在DQN中会有用到
* **Problem😣**:一般来说,Q Value对不同Action的方差小,难收敛(而且在某些情况下,Q-Value始终为正))
  * Solution:引入Advantage Function

## Sarsa(State-Action-Reward-State-Action-这个名字也太魔性了...) 
* 前向推断方式类似于Q-Learning（都是从一个QTable中选取最大Q值的策略）
* 更新方式不同：
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190908135538.png)
* 可以理解为对差距的定义不一样：
    * Q-Learning是 “下一状态的最佳Q” - “当前状态Q” （可以看做观察了下一状态会怎么样，再做判断（还有转机））
    * 而Sarsa直接对 “下一状态的Q” - “当前状态的Q” （可以看做已经做出了决定到了下一状态，不再有回旋余地）
    * Q-Learning在当前时刻只会定当前时刻的Action（决定了下一时刻的State）但是不会确定下一时刻的Action（需要看MaxQ(s',a')）
    * Sarsa的每一时刻都决定了当前时刻的Action以及下一时刻的Action
        * 每一时刻参与学习的参数有(State-Action-Reward-State'-Action')
* 这么看Q-Learning更加**贪婪**（体现在最大化maxQ）一些，如果两个模型都表现得好的话，Q-Learning能找到最近的路线，而Sarsa则会表现的相对**保守**（因为不保守的都死了）
* Sarsa属于*单步更新*的算法

## Improved-Sarsa -> Sarsa(lambda)
* 加速的Sarsa，主要就是把*单步更新*改成了*N步更新*，能够避免“原地打转”的情况
    * lambda在[0,1] - 0就退化为简单Sarsa，1就变成直到获取了reward才更新

## DQN - Deep Q Network
* 当问题复杂之后，Q-Table会变得非常大，*将以一个表格存储Q值，转为由一个NN来生成Q值*🤔（用一个NN来建模QTable的规律，可以认为是一种压缩感知？）
* DQN Work的核心(由于DQN使用NN,但是不满足*iid*(独立同分布),理论上在这个环节上不是特别严谨,所以需要一些额外的设计来保证网络的收敛)： 
    1. Experience Replay： 由于Q-Learning Off-Policy（离线学习，能学习过去以及现在的Experience，也可以是别的agent的）
        * 随机抽取过去的经历进行学习，而打乱经历之间的相关性，避免学到我们不需要的(或者说是一个随机采样,来保证随机性以尽量达成分布的**独立性**)
    2. Fixed-Q-Target
        * 使用两个结构一样的NN(Target/Estimation Network)，实际进行Q-Estimation的网络具备新的参数，而预测$$\hat{Q}$$的NN用的是比较久之前的
        * 每C轮迭代之后让target<-estimation,目的i是更好的稳定训练    
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909150807.png)

## DDPG (Deterministic Policy Gradient)
* DQN能够解决高维的离散问题,但是对于连续空间或是变化很大的Scenario不太适用
* 采用了Actor-Critic解决连续空间的问题
  * via 引入Actor输出连续的动作(或者是离散的),Critic对[s,a]打分
* 类似于DQN,也是Dual-Network,Critic和Actor各有两个NN(Target/Estimation)
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190915134806.png)
* DDPG不是直接将Estimation付给Target,而是采用了soft target update,让参数*缓慢更新*
* 有Trick是对Critc或者是Actor空间加入noise,会鼓励Actor进行Exploration,最后效果会更好

##　TRPO(Trust Region Policy Optimization)
* 解决DDPG网路参数更新的步长不确定问题
* UCB,核心在于学习的可信度
* 好数学...

## PPO (Proximal Policy Optimization)

## A3C - Asynchronous Advantage Actor-Critic
* 做到了异步,适用于分布式的平台,多个actor-critic对,每个对应了不同的探索策略(多个小队共同探索)
  * 由于异步各对之间低相关,不需要使用DQN中的Experience Replay机制来训练
* 整体架构和DDPG类似,比DDPG多了: **采用了Max(Advantage)而不是Max(Q)**
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190915142259.png)


## Multi-agent

> 2019.09.26 组会添加的内容

* 单机到多机带来的新麻烦
    * 非平稳
        * 如果采用对单一Agent进行训练，而将其他Agent当做环境的一部分；多个Agent同时对环境进行作用，导致丧失平稳性，理论不能保证最优解
        * **集中学习，分布执行**
    * 更大的状态空间
        * 单纯就是维度会变大
        * **参数共享**
    * 观测量更加分散，更难整合利用信息
    * 当不同Agent异质的时候尤为难整

## Ref
* [OpenAI SpinningUp](https://spinningup.openai.com/en/latest/spinningup/rl_intro2.html#id20)
* [Morvan's Blog](https://morvanzhou.github.io/tutorials/machine-learning/reinforcement-learning/2-1-A-q-learning/) 