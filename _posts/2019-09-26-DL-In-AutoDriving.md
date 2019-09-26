---
layout:     post                    # 使用的布局（不需要改）
title:      DL in AutoDriving           # 标题 
subtitle:   吹逼的谈资(写这一类记录非常养生)        #副标题
date:       2019-09-26              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-street.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
     - 综述
---

> 和Auto-Drivin相关的一些信息都会归并到这个post里，因为很重要，现在又学习不进去，所以记录一下

# Auto-Driving
* Taxonomy （按照难度排序）
     * 动态物体检测
          * 难点：
               1. 测距精度要求高
               2. 遮挡。朝向问题
               3. 误操作(树/行人)(卡车/集装箱) 
          * 一般是车辆和人为检测目标
          * 如果加入Tracking时候的Id切换
          * *Implemention* 
               * 一般来说的车辆检测是需要给一个3D BBox的(3D能够给出heading信息)
               * 最好对车辆和行人都给出一个ID号(需要结合Tracking，但是如果*遮挡*的话Tracking很容易GG)
                    * [为何要加Tracking](https://zhuanlan.zhihu.com/p/70268783)
               * 一般都会增加一些几何约束条件来保证准确度
                    * 比如确定车子的长宽比例
                    * 对行人限定高度
                    * 距离不能突变
               * 当不能真3D的时候也可以2.5D
                    * [单目测距](https://zhuanlan.zhihu.com/p/42085600) - 纯依靠视觉，通过画面畸变来预估距离(有一些矩阵变换的操作)
               * 一般以视觉图像为主，最后再与**激光雷达进行融合**，用于精确估计朝向以及距离
     * 通行空间检测
          * *判断当前车辆运行的安全边界与可行驶区域*主要检测别的车辆障碍，道路边沿
          * 很有趣的现象-当车辆颠簸的时候，相机俯仰角度变化，相机的标定参数映射到现实会导致空间边界*收缩或者拓张*
     * 车道线检测
          * 主要是在拐弯或者是俯仰的时候会失真
     * 静态物体检测
          * 主要难度在于小目标检测
          * Momenta在这个field有很多黑魔法一样的魔改Detection算法(比如基于Relation的)



## fracs
* v2x(vehicle to everything)车联网通信
* 超声波雷达(倒车雷达) 3~10m
     * 对应*自动泊车*的应用场景




## 
* [深度学习在无人驾驶汽车上面的运用有哪些？ - SYUI的回答 - 知乎](https://www.zhihu.com/question/51788678/answer/836372040)


