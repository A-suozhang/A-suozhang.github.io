---
layout:     post                    # 使用的布局（不需要改）
title:      多卡定点的bug调试          # 标题 
subtitle:   感恩飞哥，我是飞哥的舔狗！  #副标题
date:       2019-12-06            # 时间
author:     tianchen                      # 作者
header-img:  img/11_30/bg-road2.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 碎片知识
     - DL
---


> 虽然说是调试但是找问题和改代码都是飞哥做的，我就是张开嘴等着

# 问题描述

* 使用定点量化框子，当网络结构中有BN的时候，正常训练(设置为Fix_Auto)，如果结果设置为model.eval，那么就会崩掉，精度掉到10%
* bug只会在多卡(DataParallel)的情况下出现，单卡没问题，很神奇。

# 问题归因

* 又是batchnorm
  * 其实本质错误在定点工具对buffer的处理
* 具体内容见下图
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191127160541.png)
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191127155208.png)

# 问题后续

* 严格来说，即使目前进行了修改，修改的也是只考虑了单卡的数据分布，但是pytorch本身的BN貌似也是这么操作的

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191205180108.png)
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191205180159.jpg)

# 新的问题来了

## 目前的debug情况

|fix-grad|fix-bn|GRAD_BITWIDTH|LARGE_WEIGHT_DECAT|RANGE-METHOD| **现象** |
|--|--|--|--|--|--|--|
|n |*|*|*|*|没有什么问题|
|y|n|*|*|*|不会崩溃,但是精度上限会低一些|
|y|y|8/16/24|*|*|8比特会直接崩溃,16比特以上能够缓慢的获得比较好的性能,但是Acc振荡明显,随着bit增大逐渐变得明显|
|y|y|*|2e-4|*|(Semi的case需要大weight_decay),相比于大weightdecay,小的weightdecay能够苟住一段时间,但是最后还是会崩溃|
|y|*|*|*|MAX/SWEEP| SWEEP可以解决有一些离群点的问题,性能相比MAX会好一些,但是不能解决崩溃的问题



# NICS_FIX_PYTORCH

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191205220652.png)

* 主要库 ```import nics_fix_pt as nfp```
* nnf就等价与nn
 * ```import nics_fix_pt.nn_fix as nnf```
* 对每个model要做一个```model.set_fix_method(fix_method)```
 * fix method有三种
   * FIX_AUTO/ FIX_FIX / FIX_NONE
* 这样定义model
 * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191105211057.png)
* ```net.fc1_fix_params['weight']['method']```
  * 这一步操作在model.train()之前
  * **这个东西是要作为nnf.Linear(nf_fix_params)加入的**
  * 两层都是dict,里面存着Config的信息
  * 第一层就是generate config里的names,也就是weight/bias以及其他
* ```net.print_fix_config()```
  * 一次性打印下所有的config  

---

* 如果已经有了网络结构，可以通过```from nics_fix_pt import register_fix_module```来register以获得定点模块
