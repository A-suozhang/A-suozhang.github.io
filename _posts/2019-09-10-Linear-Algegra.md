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
    * 如果增加一个新的向量，对**扫出的空间(Span)**没有增加，则他与原先的基*线性相关*
        * 可以被之前的Vector线性表示（在之前向量组成的span内）
* Basis - 基
  * 可以决定一个空间

## Matrix
* 与Linear Transforamtion
  * 特性: 1.原本的直线必须仍然是直线 2.原点的位置不变(满足了1不满足2的叫做*仿射变换Affine Transform*)
  * 可以由一个矩阵来确定
  * 而且我们只需要i与j(二位欧式空间中的基)的变化就可以确定这个变换(矩阵)
* 将矩阵乘法分解
  * 矩阵的列看做是变化之后的**基向量**
  * 结果看做是它们的**线性组合**
$$
\begin{bmatrix}
{a}&{b}\\
{c}&{d}\\
\end{bmatrix}
\begin{bmatrix}
{e}\\
{f}\\
\end{bmatrix}
=
\begin{bmatrix}
{ae+bf}\\
{ce+df}\\
\end{bmatrix}
$$
  * and The Other Way Around - *通过变化前后(1,0)(0,1)向量的位置来表示变换矩阵* 
    * 比如逆时针旋转90
      * (1,0) -> (0,1)
      * (0,1) -> (-1,0)
      * 第一列表示新的X坐标
       
$$
M_{Transform}=
\begin{bmatrix}
{0}&{-1}\\
{1}&{0}\\
\end{bmatrix}
$$
    * 又比如斜切变换(空间被扭曲辣)
$$
M_{Transform}=
\begin{bmatrix}
{1}&{1}\\
{0}&{1}\\
\end{bmatrix}
$$
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910194717.png)
    * 当第一列和第二列为倍数(线性相关了),它们(*看做基*)所描述的空间将会退化为一维,该矩阵变换也就会将所有二维平面上面的点映射到一条直线上

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910194807.png)



# Refs
1. [3Blue1Brown's Video](http://www.bilibili.com/video/av6731067?p=1&share_medium=android&share_source=qq&bbid=XY8437382A43059E8474C51AA1E313CA89074&ts=1568085084206)
