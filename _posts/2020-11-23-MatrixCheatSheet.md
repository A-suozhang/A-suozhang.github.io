---
layout:     post                    # 使用的布局（不需要改）
title:      Matrix Cheet Sheet           # 标题 
subtitle:   我一定得好好记一下…       #副标题
date:       2020-11-22            # 时间
author:     tianchen                      # 作者
header-img: img/diffusion/disco-9.png  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 课程
---

# Matrix Cheet Sheet

- 针对某个矩阵A,可能有以下的性质：

1. 对称(Symmetric) - a_ij = a_ji
2. 可逆/非奇异(Non-singular) - full rank
3. Hermite/自伴随/共轭对称 - a_ij = a_ji^\*
	* related to 伴随矩阵 - 元素的共轭
4. 正定(Positive-definate)
	* all \lambda > 0
	* z\*Az > 0
	* 顺序主子式 - 位于对角线上的代数余子式(除去某行某列/(-1)^{i+j}/Sum the rest) M_ij > 0
5. 正交(Orthogonal) - U^{T} = U^{-1}
6. 可对角化-单纯(Normal) - \lambda无重根
	* (充分) 正规(AA\*=A\*A) -> 单纯
	* (充分) 幂等(A^2=A) -> 单纯
7. 正规(Normal) - A^\*A = A^\*A


### 共轭转置 (Conjugate/Hermitian Transpose)
* $$ A*_{i,j} -> \overline{A_{j,i}} $$

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

* 实对称矩阵 - Hermite*矩阵 - 正规矩阵

### 对角矩阵 (Diagonal)

* 仅对角线上有元素

#### 可对角化 - related to 相似

* <span style="color:red;">针对方阵
* $$ \sum = P^{-1}AP $$
* 存在条件: 对(n,n)方阵，特征值度数相加等于方阵大小n(注意和矩阵是否满秩无关！)
	* Det是各特征值的积
* 所有特征值的代数重数等于几何重数
	* 特征值全都不同的时候，充分条件


* 相似 - 对应的是 B = P^{-1} A P

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
* 实数应该都正规(?)
* 能够经过[酉变化](#酉(幺正)矩阵\ (Unitary))之后变为对角阵

### 酉(幺正)矩阵 (Unitary)

* $$ U^*U = U^*U = I $$
* (充分)一定是[正规矩阵](#正规矩阵\ (Normal))

#### 幺正对角化 (Unitary Diagonalisation)

* $$ A = UDU* $$

### 非奇异(Non-Singular)

* $$ |A| != 0 $$
* 非奇异 == 可逆
* 满秩为充要
* 其反向是退化(defective)(奇异矩阵)，以及降秩

### 逆矩阵 (Inverse)

* 仅对方阵
* $$ AB = BA = I $$
* 求逆 - $$ S^{-1} = 1/Det(S) S^T $$
	* 其中C为代数余子式
- 广义逆矩阵 - Moore Penrose伪逆(A+):
	* 与解线性方程组息息相关 


### 特征值与特征向量(EigenValue & EigenVector)

- `Ax`的含义为对于一个vector x，用A的特征向量作为基底表示，并且apply矩阵A所代表的线性变换
	* Ax = vx的本质是找到一种方式让把矩阵A的效果等效为一个vector,让det(\lambda\*I-AA)=0可以看作对空间进行了"压缩"
	* 特征向量的组合是“基变换矩阵” - Change of basis matrix,经过变换基之后的矩阵是Diagonal(其元素为\lambda),只对空间进行伸缩Scaling变换，将NxN维的变换退化为了Nx1的Scaling
- 逆矩阵的特征向量与原一样，特征值为1/\lambda
- 各特征向量线性无关(因为他们组成了一组基底)
- 针对某个特征值\lambda,其重数(Multiplicity)
	* 代数重数: 在特征多项式中\lambda的次数
	* 几何重数: 其对应的EigenSpace (A-\lambda_1\*I)的维度m1
		* 一个有趣的特征值的几何重数不满的情况就是计算得到的EigenVec有某一维度是任意值
		* 每个\lambda都对应着无数多个eigenVec(乘一个常数c)，理想情况(可对角化)eigenVec所Span成的空间应该是N维(特征值\lambda的次数)是一条线
	* 代数 >= 几何
	* 当所有\lambda代数等于几何的时候，可对角化
	* 其实每个lambda的代数重数表示了其对应的Jordan块的size(当每个都为1的时候，Jordan型和diagonal一致)v
		* 对N个eigen，丢了d个eigenvec(eigenVec不满维度)，Jordan Canonical Form就有n-d个1在对角阵上
- 最小多项式 - 特征多项式的一个因子(\lambda为其根/首系数为1/次数最低)
	* det(\lambdaI-A)
	* 不变因子:
		* $$ d_i(\lambda) = \frac{D_k{\lambda}}{D_{k-1}{\lambda}} $$
		* 标准型对角线上的非零元
		* 两个行列式因子的商
		* Smith标准型（对角阵）的对角元 - Smith标准型可以看作不断累积乘上特征值
	* 初等因子：是一个多项式
		* 相同是矩阵相似的充要 -> 也等价于最小多项式相同
		* 把不变因子拆分成每个\lambda一组的情况，可以重复出现，重复几次就写几次
- 应用
	* 解ODE


---

- 相似矩阵
	* \lambda都相同(必要不充分)，但是eigenVec不一定需要相同
	* 初等变换不改变\lambda -> 初等变换前后的矩阵相似


### 行列式(Determinant)

- 物理意义: 各个行Vecotr所Span成的平行六面体的体积
- 等于各个\lambda的积
- 不等于0的时候可逆

### 标准型(Canonical Form)

- Smith
	* 对角元因子都是d_{i}(\lambda) - 其中下一个可以整除上一个
	* 对角元是不变因子，可以分解得到不变因子
	* 利用其可以找Jordan标准型
- Jordan
	* 对于不能对角化的矩阵的一种surrogate,对角元为\lambda，对角元上面可能会有1
- Frobenius

### 几个定理

- Hamilton-Carley: det(\lambdaI-A) = 0:
	* related to 零化多项式，最小多项式

### 矩阵分解

1. QR分解 - 其中Q为正交(A^T = A^{-1}), R是一个上三角阵
	- 只要非奇异就可以唯一存在

方法有: 基于Gram-Schmit正交的方法，正交基的归一化形式组成的矩阵就是Q，而其系数矩阵就是R；
Givens变换，基于初等旋转； Householder矩阵，基于基本反射矩阵

2. Schur分解: 经过Unitary(所对应的变化矩阵是UU^\*=I)变化将任何的矩阵**相似**到一个上三角阵,对角元为\lambda
	- 其中Unitary换成正交的也同理
	- A为正规，则相似于对角
	- A为Hermite，也相似于对角(这种情况下就是谱分解)
	- A为实对称，\lambda都是实数

3. 满秩分解 A=FG
	- 与初等行变换相关, Hermitian标准型

4. 奇异值分解:
	- 与特征值相关，研究的是AA^H的性质

5. 谱分解: A = sum{\lambda_{i}E_i}
	- 把矩阵A分解为一系列幂等矩阵(E_i)与特征值\lambda的加权值
	- 代表着投影变换

