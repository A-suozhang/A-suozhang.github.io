---
layout:     post                    # 使用的布局（不需要改）
title:      赵赵的奇妙梯度下降          # 标题 
subtitle:   魔幻炼丹  #副标题
date:       2019-11-24            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-xueyuanroad1.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 碎片知识
---


# 前因后果

* 我发现我的weightdecay参数从2e-4设置为了4e-2,居然对收敛起到了很好的效果?
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191124220630.png)
* 调了一天别的参数发现一直出问题,但是改回来之后友好了
* 分析
  * 首先我删除了dropout,改用了weightdecay还是比较大的weightdecay,起到了防止过拟合的作用(非常合理)
    * 因为我在狗一样的迭代,而我的训练数据很少
    * 其次1000label更容易炸穿也说明了这一点
* 炸穿为什么炸的这么完全
  * 我用了logit来计算尼玛的consist loss,小炸会被迅速放大为大炸
  * 当consloss很大的时候,会影响clfloss,让clf也朝错误的方向走去,又会继续加剧consloss的增加,vicious circle
  * 炸的最核心原因是过拟合,给出meanconf很高也可以侧面反应这个

# 炼丹的同时感性理解一下SGD吧

## WeightDecay

* 李宏毅老师的课件
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191124222012.png)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191124222048.png)
* 让不重要的梯度随着训练的过程值变得更小
  * 我理解上是让所有的梯度都有一个变小的趋势,如果某参数需要变大的话,那就拉它变大,如果被放任不管的梯度就会自己萎缩掉
* 标准观点是这个相当于做了一个*L2 regularization*
* ~~有人认为weight decay其实是在拉住网络的参数,不让其偏离起初太远~~
  * 我觉得不太有道理


## Momentum

> 参考了[在神经网络中weight decay起到的做用是什么？momentum呢？normalization呢？ - 李振华的回答 - 知乎](https://www.zhihu.com/question/24529483/answer/143702691)

* 动量方法其实本质上是[共轭梯度法]((https://link.zhihu.com/?target=http%3A//www.sciencedirect.com/science/article/pii/S0893608003001709))的一种近似
  * 通过对历史梯度信息的搜索,避免反复地来来回回(消除相反)同时鼓励加速同一方向
  * 消除来回震荡
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191124221754.png)
  * 使用momentum的优化方法是Newton方程/Hamilton方程的二阶近似
    * **可以证明可以加快收敛速率,允许使用更大的LR**
    * 对上面这个观点有人认为不一定对收敛到最优有好处,甚至反而是坏的
* 有人认为其其实是一个"粘性因子"(从moving average的角度看确实是这样)

* Momentum能不能很好帮助跳出局部极值还待商榷
  * 顺便还看到了一个代表局部极值的经典函数　Ackley(密恐杀手)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191124223141.png)
  * 顺便扯开了,在[有哪些学术界都搞错了，忽然间有人发现问题所在的事情？ - 李振华的回答 - 知乎](https://www.zhihu.com/question/52782960/answer/133724696)中谈到了
    * 人们起初以为阻碍神经网络收敛的是局部最优(但是其实是鞍点)--后来人们发现根本没有那么多局部最优
      * 两者区别在于:虽然梯度都是0,但是鞍点附近Hessian矩阵的特征值有正有负,可以是不定的(而局部极值点是正定的)
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191124223543.png)
      * 原因在于低维情况下只需要考虑局部极值,大家用低维度思想去理解高维了
    * 鞍点也是驻点,鞍点附近的梯度很小
      * 鞍点和局部极值的区别在于,只是鞍点往周围的梯度很小,变化幅度变小看起来像是卡主了,但是理论上用sgd之类的方法是可以跳出来的
      * 直观的看加一些扰动,可以跳出来(也就是解释为什么adam可以跳出来的原因),但是作者提出在高维空间可能这个平坦区域很大
    * 牛顿法(代表二阶梯度)理论上一定能够好于SGD
    * SGD理论上是利用了数据分布之间的跳动性给梯度下降过程带来了随机性,对最后的泛化起到了好的效果
      * 作者认为Adam,Adadelta这中方法本质上还是SGD,虽然尝试了对牛顿法去近似,理论不能保证高于SGD