---
layout:     post                    # 使用的布局（不需要改）
title:      Matrix Cheet Sheet           # 标题 
subtitle:   我一定得好好记一下…       #副标题
date:       2020-11-22            # 时间
author:     tianchen                      # 作者
header-img:  img/2020/skate.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 课程
---

# Matrix Cheet Sheet

### 共轭转置 (Conjugate/Hermitian Transpose)
* $$ A^*_{i,j} -> \overline{A_{j,i}} $$

* $$ A^H = A^* = (\overline(A))^T $$

$$\begin{bmatrix}
{3+i} & {5} \\
{2-2i} & {i} 
\end{bmatrix}
->
\begin{bmatrix}
{3-i} & {2+2i} \\
{5} & {-i} 
\end{bmatrix}$$

### 对角矩阵 (Diagonal)

* 仅对角线上有元素

#### 可对角化

* <span style="color:red;">针对方阵
* $$ \sum = P^{-1}AP $$
* 存在条件: 对(n,n)方阵，特征值有N个根

### 正交矩阵 (Orthogonal Matrix)

* $$ QQ^T = I $$
* 正交矩阵为实的特殊情况是[幺正(酉)(Unitary)矩阵](#酉(幺正)矩阵\ (Unitary))  ( $$ U^*U = UU^* = I $$ )
* 行和列均为正交的单位实向量

### Hermite矩阵 (自伴随矩阵)

* $$ A = A^* (a_{i,j} = a_{j,i}^*) $$
* 对任意矩阵，AA^*都是[Hermite矩阵](#Hermite矩阵\ (自伴随矩阵))且至少半正定
* 对角元为实数，EigenVaue都为实数，EigenVector相互正交(构成正交基)
* 实对称矩阵是[Hermite](#Hermite矩阵\ (自伴随矩阵))的实数情况下的特例

### 正定矩阵 (Possitive-definite)

* (必要)[Hermite矩阵](#Hermite矩阵\ (自伴随矩阵))，且特征值均为正数
* 正定中的正数退化为非负数，则为半正定


### 正规矩阵 (Normal)

* $$ AA^* = A^*A $$
* 能够经过[酉变化](#酉(幺正)矩阵\ (Unitary))之后变为对角阵

### 酉(幺正)矩阵 (Unitary)

* $$ U^*U = U^*U = I $$
* (充分)一定是[正规矩阵](#正规矩阵\ (Normal))

#### 幺正对角化 (Unitary Diagonalisation)

* $$ A = UDU* $$

### 非奇异

* $$ |A| != 0 $$
* 非奇异 == 可逆

### 逆矩阵 (Inverse)

* 仅对方阵
* $$ AB = BA = I $$



