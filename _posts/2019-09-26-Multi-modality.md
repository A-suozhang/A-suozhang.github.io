---
layout:     post                    # 使用的布局（不需要改）
title:      多模态学习 Multi-Modality-Learning           # 标题 
subtitle:   真正的Sensor Fusion        #副标题
date:       2019-09-26              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-nmb-corner.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 综述
---
# ⌛ 多模态 Multi-Modality 

> ⌛ 表示待补充

* 模态： 信息的来源或者是形式
* 分类 Taxonomy
    * 多模态表示学习(Multimodal Representation)
        * **Representation**意指的将输入数据的Feature映射为高维度向量
    * 模态之间转化 (Translation)
        * 映射关系的转化
        * *Example*：语言翻译，Caption，
    * 模态对齐 (Alignmnet)
        * 主要是寻找不同模态之间的对应关系
        * *Example* 视频处理中的Temporal Sequence Alignment
            * 甚至语义分割也是?（没理解）
    * 多模态融合 (Multimodal Fusion)
        * 常分为不同的层次
            * Pixel Level
            * Feature Level
            * Decision Level
        * *Example*最终一般是分类预测或者是回归 
            * 情感分析
            * 生物特征识别
    * 协同学习(Co-Learning) 
        * 各个模态之间的信息互相补充
        * 引申到*迁移学习*，以及*One/Zero Shot*


## 待补充 ⛏

## Refs
* [Multimodal machine learning: A survey and taxonomy](https://arxiv.org/abs/1705.09406)
* [CMU ACL2017 Tutorial](https://www.cs.cmu.edu/~morency/MMML-Tutorial-ACL2017.pdf)



