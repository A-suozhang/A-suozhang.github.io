---
layout:     post                    # 使用的布局（不需要改）
title:      数学公式应该怎么打              # 标题 
subtitle:   顺便学习一下LaTex哈        #副标题
date:       2019-09-09              # 时间
author:     tianchen                      # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 常用
    - 博客
---

# Math Formula
* 起因是看OpenAI的RL Spining Up 当中有太多公式了，实在忍不住配置了一下Markdown和博客上面的数学公式
* 顺便可以学习一个LaTEX的公式打法
  
# 语法
## 1. 希腊字母　   
$$ \sigma - \xi $$
* 如果需要大写字母，把首字母大写　$\Omega \omega$
* 斜体字母在前面加上＂var＂    $\varSigma$
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909095145.png)

## 2. 装饰
2.1 上标　"^" ; 下标 "_"   
$$ \alpha^2  \omega^{20}$$
2.2 矢量
$$ \vec{xy} $$
2.3 括号
* "\left" "\right"让符号的大小与临近的公式适应
$$ \left(\frac{x}{y}\right)$$
$$ (\frac{x}{y})$$
```$$ \left(\frac{x}{y}\right)$$```

2.4 求和 & 极限　&积分 ```OP_{lower_bound}^{higer_bound} {fx}```
$$ \sum_{i=2}^{100}(a_i*k_{ij}) $$  
```$$ \sum_{i=2}^{100}(a_i*k_{ij}) $$```  

$$ \lim_{x \to 0}{x^3+y^{10}} $$
```$$ \lim_{x \to 0}{x^3+y^{10}} $$```

$$ \int_{20*a}^{\infty}{fxdx}$$
```$$ \int_{20*a}^{\infty}{fxdx}$$```

2.5 分式根式　```\OP{}{}```
$$ \frac{x^3}{2\pi}$$
```$$ \frac{x^3}{2\pi}$$```

$$ \sqrt[3]{x^2}$$
```$$ \sqrt[3]{x^2}$$```

2.6 特殊函数 sin ln max

2.7 空格　"\ "　"＼quad"

### 3.特殊符号
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909101738.png)

### 4.方程组 & 矩阵

$$
\begin{cases}
a_1x+b_1=c_1\\
a_2x+b_2=c_2\\
a_3x+b_3=c_3\\
\end{cases}
$$

$$\begin{bmatrix}
{a_{11}}&{a_{12}}&{\cdots}&{a_{1n}}\\
{a_{21}}&{a_{22}}&{\cdots}&{a_{2n}}\\
{\vdots}&{\vdots}&{\ddots}&{\vdots}\\
{a_{m1}}&{a_{m2}}&{\cdots}&{a_{mn}}\\
\end{bmatrix}$$

