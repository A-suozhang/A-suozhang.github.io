---
layout:     post                    # 使用的布局（不需要改）
title:      OnlineLearning内容梳理           # 标题 
subtitle:            #副标题
date:       2020-02-01            # 时间
author:     tianchen                      # 作者
header-img:  img/1_28/nantong-6.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
---

# Understanding and Improving One-Shot NAS

* 场景是One-shot， one-shot节省资源，但是ranking correlation差
* 这篇文章认为问题在于one-shot training和full training的差异性
* 分析了Gap的存在原因NAO-V2来减少这个gap
  * 增加每个独立架构的平均update次数
  * 对于更加complex的结构增加update次数
  * Make the one-shot training of the supernet inde- pendent at each iteration

## Related Fields

* One-shot NAS
  * hyper-network trained once
  * Sample Sub-Net and share weight
    * ENAS - Weight Sharing Search by RL
    * NAO
    * DARTS - search thorugh supernet's gradient

### insufficient optimization of individual architecture
* Supernet train 100-200 epoch 
  * each step a different arch is trained
* Empirical 更容易采集出Small and slim的结构（一般包含很多non-parameter的）
  * 作者认为是small的网络在小在小的step的时候比较容易训上来，因此更容易被采样到
* NAS的架构update是从中间截断的
  * 会导致momentum和lr decay dependent of previous layer
  * 也会导致最后的结果下降

### Imbalanced Training

### Training Independent

* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200201123751.png)

## Expeiments

* 为啥没有相关性的实验结果？