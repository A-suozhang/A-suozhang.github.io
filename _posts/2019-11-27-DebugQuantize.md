---
layout:     post                    # 使用的布局（不需要改）
title:      多卡定点的bug调试          # 标题 
subtitle:   感恩飞哥，我是飞哥的舔狗！  #副标题
date:       2019-11-25            # 时间
author:     tianchen                      # 作者
header-img:  img/11_30/bg-mb.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 碎片知识
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