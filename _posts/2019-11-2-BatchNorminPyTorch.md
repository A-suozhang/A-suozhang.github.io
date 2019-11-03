---
layout:     post                    # 使用的布局（不需要改）
title:      Pytorch里的BatchNorm究竟是怎么工作的?!          # 标题 
subtitle:   不讨论一下还不知道大家对这个东西的认知居然真的不一样...   #副标题
date:       2019-11-2           # 时间
author:     tianchen                      # 作者
header-img:  img/bg-cat1.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - debug
---

# Pytorch里的BatchNorm到底是咋工作的??

> 这个故事起源于我尝试对AdaBN的论文进行复现的时候获得了完全相反的结果
> 告诉我们.论文复现不出来,先反省是不是自己菜!


* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191102194152.png)
* 就是它,有这样的几个输入参数
    * Num_features - 和输入数据的Channel数目保持一致
    * eps - 保持数值稳定的一个很小的数,我们不care
    * keep_tracking_stats - 如果设置为True的话,就保存下当前的一个个Batch的均值方差的一个Runing Mean
    * Affine 有没有一个可以learn的wx+b (默认为True)
* **非常关键!!**
* Batchnorm中的均值和方差到底是什么东西呢?
* 首先,在完全默认的情况下,momentum=0.1,keep_tracking_stat = True
* 在net.train()的时候
    * **虽然在keep_tracking_stats = True**的时候,记录下了这个滑动平均
    * 但是**在训练阶段前向传播的时候这个平均值并没有被用到**,实际用的是**每个MiniBatch的均值方差**
* 而在net.eval()的时候
    * 使用的是之前记录下来的滑动平均了,均值方差不会再被更新
    * 当然如果又是eval又*keep_tracking_stats = False*的话,eval的时候也是不会使用一个滑动平均的(因为完全没有被记录下来desu)

* 所以说目前如果要浮现batchnorm在不自己写一个的情况下最直接的办法是
  * 首先train完了之后,对每个BN层调用Reset,先把里面存着的train set的东西给丢掉
  * 然后开始以train的形式做test,但是不调用optimizer.step()
  * 一定时间之后再net.eval()让他用新学到的这些个适应test set的均值方差

> 非常感谢飞哥,如果不是正好讨论到这个问题,以我的智力和能力,是不足以把pytorch源码扒到那么cpp,并且锁定真正起效果的代码在哪里的
> 我是飞哥的舔狗