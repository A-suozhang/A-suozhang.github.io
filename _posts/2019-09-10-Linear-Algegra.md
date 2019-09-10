---
layout:     post                    # 使用的布局（不需要改）
title:      重学线性代数 Linear-Algebra             # 标题 
subtitle:   一个感慨自己本科三年白学了的系列        #副标题
date:       2019-09-10              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-beer.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 重新学习
---
# 起因
* 保研面试需要复习一些科目，对我来说，复习基本就属于重学了，也算从一个不一样的角度入门学习
> 给自己几条纲领：：轻证明，重串联 轻定义，重理解 （希望自己能把这个方式Carry Out了）
> Make It **REASONABLE**
* 遥记得大一的时候去T大和学长聊天他就说过他自己把线代学了三遍
* 目前我对线代的理解近乎为0吧，大一啥都没学到，特此重学

# Linear Algebra
## Vectors 向量
* **What Is Vector ？** Mathematica View Of Vector: 任意乘加起来有意义的东西都可以是vector
    * 比如我们的Feature Vector，物理意义比较抽象
    * *由性质(属性？)来定义概念*

$$ 
\begin{bmatrix}
{a1}\\
{a3}\\
\end{bmatrix}
$$

* 向量加法，看做数轴上面加(减)法的二维拓展
* 向量乘法，缩放(Scaling)
* *给了计算机科学一种将大量数据格式化，概念化，并且可以加以计算的方式*
## Span 线性空间 
* The **Span** Of 2 Vectors由两个向量“张开成的空间”
    * 可以理解为一个固定不动，另一个变化所“扫”出的空间(多个也同理)
    * 如果增加一个新的向量，对扫出的空间(Span)没有增加，则他与原先的基*线性相关*
        * 可以被之前的Vector线性表示（在之前向量组成的span内）
* Basis - 基
    * 




# Refs
1. [3Blue1Brown's Video](http://www.bilibili.com/video/av6731067?p=1&share_medium=android&share_source=qq&bbid=XY8437382A43059E8474C51AA1E313CA89074&ts=1568085084206)
