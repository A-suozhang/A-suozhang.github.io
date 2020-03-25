---
layout:     post                    # 使用的布局（不需要改）
title:      Active Learning 主动学习           # 标题 
subtitle:   Tell When to Label   #副标题
date:       2020-03-20           # 时间
author:     tianchen                      # 作者
header-img:  img/1_28/dawn.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 综述
---

# Active Learning

> [知乎上的一个综述文章](https://zhuanlan.zhihu.com/p/39367595)

* Motivation: 
  * 从Unlabel数据中选中比较困难的，给专家来进行补充标注，以完成任务
  * 在实际标注的时候，我们不知道标注多少数据才能得到预期的结果，实际上只需要有大多数的就可以了(Label effiency)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200322172836.png)
* 也会被称为 Query Learning
* 使用的场景
  * 获取Label困难(比如医疗图像)
  * 注意对于一些复杂的任务，需要衡量模型本身能够解决，不然再大量的标注数据也不能达到预期结果
  * 衡量Hard Sample和Easy Sample的指标需要定义   (会不会与NAS的rank相有一些矛盾)
* 核心问题在于**Selected Strategy**



* 经过思考之后发现和NAS本身有些难以结合，暂且放下这个