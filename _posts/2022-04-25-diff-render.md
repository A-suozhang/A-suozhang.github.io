---
layout:     post                    # 使用的布局（不需要改）
title:      可微分渲染         # 标题 
subtitle:   不懂图形学  #副标题
date:       2022-04-25            # 时间
author:     tianchen                      # 作者
header-img: img/diffusion/disco-19.png  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
     - 综述
---

> 1. 尝试让博客回归他本来应该成为的样子，成为某个topic的personal knowledge stack
> 2. 由于这个markdown的前端显示和自己写markdown给所有东西都打上bullet list的习惯不符合，所以尽量在本篇博客中，少用bullet list，尽量用正文和空行来表示层级结构
> 3. 另外，今天听到了一期不错的音乐电台节目，[离谱-About Radiohead](https://www.xiaoyuzhoufm.com/episode/617bdf5872729853fd7c3934)，已经将KID A加入了需要买的黑胶唱片List


# Rendering

渲染的定义: “Rendering in computer graphics is the process of generating images of 3D scenes defined by geometry, materials, scene lights and camera properties”特点：`from 3D(mainly mesh) to 2D`

可微分渲染的定义：用end2end的方式去优化整个过程，其中的核心难点是，`obtain useful gradient`，DR的意义是能够bridge 2D and 3D model. 应用意义在于：可以用image-based的方法来训练reconstruction任务，而不需要整个pipeline。

Neural Rendering是采用NN的可微分渲染，instead of manually defining the rendering and its differentiation.(❓:手动给ray tracing定义一个backward算是这种？)

DP的两种form，一种是直接用一个NN来做render，一个是用NN来达成De-Render的过程，不过用SelfSup进行训练

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220425170841.png)

输入数据的性质形式有以下四个： 1. Shape 2. Camera 3. Material 4. Lightning； 输出形式：1. RGB 2. depth
输入数据的模式则有以下几个： 1. Mesh 2. Voxel 3. Point Cloud 4. Implicit（Neural Field Representation）

## 1. 不同数据模态的Diff Render方式

### 1.1 Mesh-based

（Mesh）： 是Graphics当中的一种很常用的表现方式，需要存储下来的部分经常有： vertices & edges & faces/polygons

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220425172608.png)

代表性表征形式：Vertex2Vertex Meshes（用每个Vertex与其相连的其他Vertex表示）

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220425173125.png)

最常用的表示：Face-Vertex mesh：用一组face以及一组Vertex表示（因为他可以显示的query每个face上的所有vertex，以及围绕一个vertex的所有face）

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220425173303.png)

**[Rasterization 光栅化](https://www.youtube.com/watch?v=t7Ztio8cwqM)  Render的前向过程** 对Mesh-based的3D input的render过程（生成某一个像素的RGB信息）：1）为这个pixel assign一个三角形，通过从3D real world space to 2D screen plane的映射，找到那个包含当前pixel的triangle，然后再选择距离camera最近的那个   2）并用这个三角形的三个角上的Vertex的颜色，计算出该pixel的颜色

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220425182232.png)


❓: Rasterization 如何deal with lightning：我理解应该对mesh的每个vertex的color应该是经过lightning model处理过brightness等之后的

**Render不可导的问题** 由于Projection步骤不可导（是discrete的choose这个triangle的，从real world到camera world的操作是可导的，就是一些矩阵变换），对DP带来了很大的麻烦，步骤二中的操作都是differentiable了，点的位置可以通过weighted sum of vertices location得到，颜色由一个reflection model得到。 

另外一个问题是derivative of pixel color w.r.t vertex position is 0, which与现实不符。有一些采用approximate gradient的工作将两者建立上关联。

**1.1.1/2: 解决方案** 对问题一，approximate 前向rasterization，对问题二，approximate反向的梯度

**1.1.3: Global Illumination**主要是stress了rasterization为了速度而无法建模某一点的lightning应该被其他点的光照反射所影响，对每个点的redering做蒙特卡洛采样。Global Illumination其实和Rasterization无关，它是一种更加advanced的ray tracing，他的特点是，不仅考虑直接来自光源的光，还考虑来自相同光源的光被其他表现反射之后的情况。因此，就建立了渲染时候不同对象的联系。

**1.1.4 Ray Tracing(虽然没找到合适的differntiable方法，但是顺便介绍一下ray tracing)**: 关键元素：相机位置，光源位置，光线从摄像机沿着某个方向出发（会穿过相机平面上的某个pixel，如下图），射到物体之后，依据物体表面材质进行反射和折射，直到到达光源。

Shading模型，表示pixel的brightness应该和入射到物体的angle相关。
Shadows模型：看光线是否能够回到光源，如果回不到，说明被block了，就应该是阴影。
Reflection模型： 如果一条光线与多个物体产生的intersection，那么需要根据物体的发射率，来用多个物体的元素加权来考虑当前pixel的颜色

与Rasterization的区别： 1） 非常Computational Expensive   2）Camera-Centric 

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220425183505.png)

- [Video1](https://www.youtube.com/watch?v=oCsgTrGLDiI)
- [Video2](https://www.youtube.com/watch?v=lKIytgt3KXM)
- [Video3(Nvidia-Not Recommended)](https://www.youtube.com/watch?v=gBPNO6ruevk)

**以上Scheme总结**  approx grad和approx rasteriazation这两种方式通过handcrafted的方式设计meaningful的gradient，efficient但是可能会产生blurry的输出。global illumination现在看起来impractical for use因为消耗太大难以应用。（将这几个并列让我感觉作者其实没有完全搞清楚逻辑关系）

### 1.2 Voxel-based

最朴素的voxel是store occupancy，对每个voxel有一个0~1之间的值。更advanced的方式是保存SDF(Signed distance function)存储voxel中心到物体表面的距离。或是还有额外的信息来denote Material材质。

可以用Volumetric Rendering的方式来渲染，对一跟ray上的所有voxel做aggregation, which is used in Nerf.

### 1.3 Point Cloud based

渲染一个point cloud分几个步骤： 1. 将3d点云投影到相机平面   2. 对每个点对pixel的影响“Influence”做定义 3. 将所有点aggregate到一起
核心在于对“influence”的定义，如果每个点到一个pixel会是一个很sparse的图片，所以会让一个点覆盖一个区域（比如套一个truncated gaussian blur）

多点aggregation的时候：为了考虑occulusion，选取最近的点做覆盖

### 1.4 Implicit Representation

example： Nerf flow

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220425204855.png)

## 2 Neural Rendering

核心是不handcraft how to differentiate, directly end2end learn a rendering process. 可以替代其他模态，但是问题是在sample points along a ray的时候很computational expensive. 


## 3. Takeaways

1. 目前的DiffRender的方法用local illumination简单，但是难以获得真实的图像； 用global illumination computational难以接受
2. Game engine中有一些real-time的render方法，但是其和diff之间的gap还没有被bridge
3. DiffRender of video worth explore




# Reference

1. [之前的Notion整理](https://cuddly-sandal-df1.notion.site/Neural-Rendering-deaf540e14a148f29a6d75ac19d754b7)
2. Survey Paper: [Differentiable Rendering: A Survey](http://arxiv.org/abs/2006.12057)
