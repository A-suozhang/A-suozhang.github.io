---
layout:     post                    # 使用的布局（不需要改）
title:      重学线性代数 Linear-Algebra             # 标题 
subtitle:   一个感慨自己本科三年白学了的系列        #副标题
date:       2019-09-12              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-beer.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 重新学习
---
# 起因
* 保研面试需要复习一些科目，对我来说，复习基本就属于重学了，也算从一个不一样的角度入门学

> 给自己几条纲领：：轻证明，重串联 轻定义，重理解 （希望自己能把这个方式Carry Out了）
> Make It **REASONABLE**

* 遥记得大一的时候去T大和学长聊天他就说过他自己把线代学了三遍
* 目前我对线代的理解近乎为0吧，大一啥都没学到，特此重学
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912151656.png)

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
  * 一个线性空间可以被它的一组基完全表示

## Matrix
* 与Linear Transforamtion
  * 特性: 1.原本的直线必须仍然是直线 2.原点的位置不变(满足了1不满足2的叫做*仿射变换Affine Transform*)
  * 可以由一个矩阵来确定
  * 而且我们只需要i与j(二位欧式空间中的基)的变化就可以确定这个变换(矩阵)
* 将矩阵乘法分解
  * 矩阵的列看做是变化之后的**基向量**
  * 结果看做是它们的**线性组合**
> 把一个矩阵的列，看做单位基向量经过它对应的线性变换之后的Landing Spot
* 将几个变换复合*先发生的靠近[x,y]（也就是在右边）*

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

## Determinant 行列式
* 行列式的意义是该空间的基，所构成的平行四边形(平行六面体)的面积(体积)
    * 矩阵或者说线性变换的行列式表示了**空间被挤压，伸缩的情况**
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912152003.png)
* *What If Det(A) < 0*
    * 当基之间的相对位置关系发生了改变的时候（比如原先j在i的逆时针方向，但是后来跑到顺时针方向了）
    * 当整个空间(2-D)被压缩到一条直线的时候(这个时候ij重合了),此时det(A)大小为0s
* 数学定义的几何意义
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912152111.png)
* 当**行列式**的值为0的时候，平行四边形将会退化为直线（2-D -> 1-D）
    * *这个时候没有逆变换* （不满秩的时候（当维度退化的时候）不存在逆变换）
        * ❗没有办法从一个低维度的空间回复出一个高维度的空间（这样一定会让1个input对应多个output，而函数要求单输入单输出）
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912152314.png)
* **秩**： 该线性变化的矩阵的列向量，所Span成的空间，也叫做**列空间**的维度，就是这个矩阵的“秩”
    * **满秩**该线性变换不会将空间退化 -> 行列式的值不为0 -> 存在逆变换
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912152415.png)
* 将**线性变换** -> **矩阵** -> **线性方程组**所对照的话
    * 当满秩的时候，直接对应，很简单（一定有解）
    * 当不满秩的时候，只有特殊情况才有解（当解正好在被退化的平面上时）
        * 秩大小不同，解空间也不同： 比如3-D的，退化为2-D（秩为2）解就在平面上，如果rank=1，那么解收缩为原点
        * 原点，又被称为**零空间(Null Space)**或者是**核(kernel)**
* *不为方阵的矩阵* - *Transorm Between Dimensions*
    * 本身就是跨维度的变换，不存在Det
    
$$
\begin{bmatrix}
{a_{11}}&{a_{12}}&{a_{13}}\\
{a_{21}}&{a_{22}}&{a_{23}}\\
\end{bmatrix}
$$

    * Example: 3 columns表示输入空间有3个基底（也就是3维）
        * col - input； row - output
        * *有一些类似于投影或者点积的意味但是又区别*
## 点积 (Dot Product)
* **经典看法**对两个维度一样的向量，将对应位置的元素相乘并且相加
* **几何意义**  $$ \vec{a} \cdot \vec{b} $$ a在b上（b在a上）的投影乘上它本身的长度 
* 对偶性
* 多维到1维(数轴)的线性变化
    * 套用我们前面的，将一个向量在它的空间内（N维）分解为N个基底的表示，并且用每个基底的变化后的坐标组合得到这个点变化后的坐标
        我们会发现这样的过程，就是做点积的过程
* **新的看法** 假设有一个2-D -> 1-D的矩阵（1*2） 
    * 这个新空间是一条数轴，假设它在2D平面内的一条直线
    * 它的两个值分别是i，j（二维空间基底）映射到这数轴上对应的点的值
    * 假设数轴上单位长(1)的值对应二维空间中的点为(ux,uy)
        * 有对称性可以得到这个矩阵就是 [ux,uy] 
        * (想象i投影到数轴上，由**对称**就相当于u投影到x轴上)
* **对偶性(Duality)看法**
    * 两个向量的点乘，将其中一个转化为线性变换 （线性变换与向量之间具有对偶性）

$$ \begin{bmatrix}{x_1}\\{y_1}\end{bmatrix} \cdot  \begin{bmatrix}{x_2}\\{y_2}\end{bmatrix}\quad >>> \quad \begin{bmatrix}{x_1}&{y_1}\end{bmatrix}\begin{bmatrix}{x_2}\\{y_2}\end{bmatrix}$$ 

> 不要把Vector(向量)看做空间中的一些矢量，而是看做线性变化的物理载体

## 叉积
* **经典看法** 模等于两个向量围成的平行四边形的面积（如果b在a的右边（顺时针）结果为正）
    * 如何记顺序: ixj>0
    * 右手定则
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912192336.png)
* Where Did The Det Come From :
    * 延伸之前对Duality的看法： *根据ab向量定义一个三维到1维度的线性变换，找出这个线性变换所对应的Vecotr就是结果*
    * 将3x3矩阵的第一纵列看做变量，构造一个函数，这个函数是线性的，可以看做是由线性变换组成的，再由对偶性，将其转化为点积(dot)
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912193435.png)
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912193540.png)

## 基变换
* 基底的不同可以看做 1)先进行基向量变化 2)再看向量
    * *注意这边基底的变化应该在观察的变化之前* 
    * *注意正逆哦，可以想一下旋转*
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912195538.png)
* 如何描述不同基底下的变换矩阵？
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190912200128.png)
    * （把(-1,2)替换成她的基，前面三个的积就是所求的矩阵）
    * $$ A^{-1} M A $$ The New Transformation M From Another Aspect (A)

## 特征向量特征值(Eigen Vector&EigenValue)
*  一般的一个很Random的线性变化，大部分向量都会偏移自身span出的直线，那些例外的，就是**特征向量**，而其伸长(压缩)的就是**特征值**（如果为负数就是方向反了flipped）
* 定义： $$A\vec{v}=\lambda\vec{v} \quad>>\quad A\vec{v}=\begin{bmatrix}{\lambda}&{0}&{0}\\{0}&{\lambda}&{0}\\{0}&{0}&{\lambda}\end{bmatrix}\vec{v}\quad>>\quad (A-\lambdaI)\vec{v} = 0 $$
    * 且只有当矩阵不满秩的时候才会存在解使得变换降维 
* 特征基 此时Matrix为Diagonal Matrix(只有对角元)
    * 可以自己转 - 这个过程就是**特征值分解 **

        


# Refs
1. [3Blue1Brown's Video](http://www.bilibili.com/video/av6731067?p=1&share_medium=android&share_source=qq&bbid=XY8437382A43059E8474C51AA1E313CA89074&ts=1568085084206)
