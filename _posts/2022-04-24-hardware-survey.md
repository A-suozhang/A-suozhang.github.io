---
layout:     post                    # 使用的布局（不需要改）
title:      Personal Survey of Hardware Acceleration of Neural Network           # 标题 
subtitle:   个人总结一下认真写一个持续更新的post  #副标题
date:       2022-04-24            # 时间
author:     tianchen                      # 作者
header-img:  img/wallpaper/shigatsuwa.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
     - 综述
---

> Eff Deep Learning与Hardware Accel相关的一些自己不成熟的想法的梳理

# 工作的几个层面


## 1. 算法(软件)层的加速

> 我们熟悉的Pruning，Quantization，NAS等等都可以认为是该种方式，主要特征是：获得计算量或者存储的节省

- 例子：我们熟悉的几乎所有

- 表现出来的指标： `FLOPs，Param Size，GPU end2end latency`

- 但是这里的一些计算存储的优化，并不能直接反应到硬件设计的效率，why？

首先，一些软件层面的优化，不会直接反馈到硬件指标。举个例子，比如对于FPGA加速器，你减去了一半的Channel，纸面上减少了一半的显存和计算。但是实际上在部署时候，本身模型还是大到不能存到片上，
所以只是多算了几个时钟周期，可能对端到端的Latency/吞吐量throughput有优化，但是对计算效率Energy Efficiency并没有提升。（以上两个部分分别代表了硬件加速器的两大指标：速度和能效）

甚至，对于一些硬件设计上本身就不是瓶颈的case，优化某些操作对硬件效率完全不会有提升。比如对于某些层的剪枝，由于存在shortcut的结构，本身硬件上计算需要等待其他流程的数据done，卡在idle，对它做了优化，并不一定能够提升硬件效率。或者对于一个模型，降低了显存和计算之间的比例，但是这本身是一个计算bounded的任务，减少内存消耗并不能提升硬件效率。

## 2. 软硬件协同优化

> 硬件感知（Hardware-aware）的算法优化或者是协同优化（涉及到硬件架构中一些设计的优化，但是不是pure硬件优化）

- 例子：[Hardware-aware Pruning/NAS的工作，如NAAS](https://arxiv.org/abs/2105.13258) [PIMNAS](http://nicsefc.ee.tsinghua.edu.cn/%2Fnics_file%2Fpdf%2Fb6a3130a-52cc-4896-9cb5-d34b56adc968.pdf)

- 指标：(与硬件加速器指标类似且相关)
    - 算法中的一些指标：FLOPs，Param
    - High Speed：高throughput(IPS): 每s的inference次数（单位：s^{-1}）; 低Latency
    - 能效： Energy Efficiency (GOP/J) Memory Efficiency（J/Byte）

- 该层面优化的一些层次？


# 3. 硬件层面的优化

### 3.1: 针对特定硬件平台(GPU/FPGA)的优化

### 3.2: DSA(Domain-specific Architecture)设计

# Surveys

### [ACM TRETS 19] A Survey of FPGA-Based Neural Network Inference Accelerator

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424100631.png)

- Hardware Optimization
    - Computation Unit Design： 低比特的计算单元，或者Conv的变体
    - Loop Unrolling strategy： 目的： increase parallelism & save computational waste
        - Compiler的优化在这个层面？或者是这个层面的一个generalized？（TVM/MLIR）
        - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424102020.png)
        - Loop Tiling是Loop Unrolling的一种更高层次的,Loop tile将unrolled好的数据给排布好成一个个的tile。loop tile的size可以调整计算存储比
        - Loop interchange决定了tile的计算order
    - Data Transfer Design & On-chip memory design
        - systolic array is an example
        - data reuse for high parallelism: 
            - too much broadcast: high fan-out & high routing time: lower the working frequency
            - 在这里需要获得一个 locality & parallelism之间的trade-off？
    - Cross-layer Scheduling：
        - fuse many layers together
    - 我们在compiler中提到的scheduling？和这些组件的关系？
    - 以及cache的locality？
        - cache的locality描述的其实本质是一个对memory的读取速度和data pattern有关的问题。
        - 能够反映到数据带宽(bandwidth)，sequential且规则的DDR access的访问带宽，肯定比random access更快*


- System Diagram：
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424102713.png)

- Roofline Model：
    - X axis： CTC： Computation to Communication Ratio
        - number of operations with a unit size of memory access
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424103108.png)


### Efficient Deep Learning: A Survey on Making Deep Learning Models Smaller, Faster, and Better

- HPO
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424113413.png)

- Infrastructure:
    - Tensorflow系列
        - TensorLite: 针对ARM-based processor做优化（以及针对Qualcomm的DSP）
            - 提供了一个解释器Interpreter
            - 提供了一些算子： OP Kernel
            - 提供了converter实现了计算图的量化等
        - TFMicro： for IOT microcontrollers
        - TF.JS: train model on browser
    - XLA： server-side acceleration:
        - a graph compiler
        - customized kernel for graph, fused operations
    - Pytorch JIT(Just-in-time) Compilation:
        - 类似XLA的功能,在其中的替代品是GLOW和TensorComprehension
        - 生成 a serializable IR(high-level IR)
            - 包含了fusion of pointwise operations

- GPU:
    - software libraries like cuBLAS，cuDNN
    - TensorCore 针对DL应用的硬件单元（可以switch多种bitwidth）

- TPU：
    - 属于ASIC，不需要cater to除了DL之外的其他应用， which GPU has to
    - Systolic Array Design
        - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424115852.png)
    - EdgeTPU： ASIC for edge devices, 针对的平台是Raspberry-Pie，Pixel Smart phone