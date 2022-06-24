---
layout:     post                    # 使用的布局（不需要改）
title:      稀疏卷积计算     # 标题 
subtitle:   缝合一下几篇post  #副标题
date:       2022-06-24            # 时间
author:     tianchen                      # 作者
header-img:  img/wallpaper/radiohead.png  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
---

# 3D稀疏卷积流程

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/IMG_5359.jpg)

- 输入 Sparse Voxel形式：
    - Coordinates: [N,3]
    - Input Voxel Idx: [0,1...,N]
    - Feature: [N,C]
    - Kernel Feature: [3x3x3,C_in,C_out]
- Input Sparse Map可以由上述推得（但是不显示存储），Output Sparse Map有2种形式
    - SparseConv： 存在非稀疏度的扩张（输入只有一个中心点有feature，但是输出的时候以它为中心的3x3x3都有feature）
    - Submanifold： 与input sparse map一致

1. 构建Input-Output Coord之间的Hash Mapping
    - (对于submanifold，这个mapping是一一映射，所以不需要构建hash table)
    - 如上图中，两个输入点，分别输出扩张到4个和6个位置，构建一个hash map将这些部分映射到输出的9个位置（2d 3x3一共有这么多输出可能点）

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220624141326.png)

2. 构建Rulebook（Kernel Offset -> （input voxel id, output voxel id）的mapping）
    - 这一过程类似2d conv之间的im2col，本质上是把输入的kenerl给unroll了，loop的最外层是kernel offset（3x3x3）
    - 对上一步建立的input output coord mapping计算kernel offset
    - 并且对kernel offset为query建立hash map

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220624142839.png)

3. 构建好Rulebook之后的整体的计算flow   

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220624142858.png)


---

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220624143319.png)

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220624143301.png)



# Reference

- [How does sparse convolution work?](https://towardsdatascience.com/how-does-sparse-convolution-work-3257a0a8fd1)
- [torchsparse](https://torchsparse.mit.edu/)