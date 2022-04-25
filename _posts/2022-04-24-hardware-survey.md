---
layout:     post                    # 使用的布局（不需要改）
title:      Personal Survey of Hardware Acceleration of Neural Network           # 标题 
subtitle:   个人总结一下认真写一个持续更新的post  #副标题
date:       2022-04-24            # 时间
author:     tianchen                      # 作者
header-img:  img/wallpaper/Solitude.jpeg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 论文阅读
     - 综述
---

> Eff Deep Learning与Hardware Accel相关的一些自己不成熟的想法的梳理

# 工作的几个层面


## 1. 算法(软件)层的加速

> 我们熟悉的Pruning，Quantization，NAS等等都可以认为是该种方式，主要特征是：获得计算量或者存储的节省

- 例子：我们熟悉的几乎所有Paper

- 表现出来的指标： `FLOPs，Param Size，GPU end2end latency`

- 部署实例：如TF，Torch自带的一些轻量化方式(如torch中的quantize)

❓： 但是这里的一些计算存储的优化，并不能直接反应到硬件设计的效率，why？

首先，一些软件层面的优化，不会直接反馈到硬件指标。举个例子，比如对于FPGA加速器，你减去了一半的Channel，纸面上减少了一半的显存和计算。但是实际上在部署时候，本身模型还是大到不能存到片上，
所以只是多算了几个时钟周期，可能对端到端的Latency/吞吐量throughput有优化，但是对计算效率Energy Efficiency并没有提升。（以上两个部分分别代表了硬件加速器的两大指标：速度和能效）

甚至，对于一些硬件设计上本身就不是瓶颈的case，优化某些操作对硬件效率完全不会有提升。比如对于某些层的剪枝，由于存在shortcut的结构，本身硬件上计算需要等待其他流程的数据done，卡在idle，对它做了优化，并不一定能够提升硬件效率。或者对于一个模型，降低了显存和计算之间的比例，但是这本身是一个计算bounded的任务，减少内存消耗并不能提升硬件效率。

## 2. 软硬件协同优化

> 硬件感知（Hardware-aware）的算法优化或者是协同优化（涉及到硬件架构中一些设计的优化，但是不是pure硬件优化）

- 例子：[Hardware-aware Pruning/NAS的工作](https://arxiv.org/abs/2105.13258) [PIMNAS](http://nicsefc.ee.tsinghua.edu.cn/%2Fnics_file%2Fpdf%2Fb6a3130a-52cc-4896-9cb5-d34b56adc968.pdf)

- 指标：(与硬件加速器指标类似且相关)
    - 算法中的一些指标：FLOPs，Param
    - High Speed：高throughput(IPS): 每s的inference次数（单位：s^{-1}）; 低Latency
    - 能效： Energy Efficiency (GOP/J) Memory Efficiency（J/Byte）

- 该层面优化的一些层次？


## 3. 硬件层面的优化

### 3.1: 针对特定硬件平台(GPU/FPGA)的优化

> 其实也可以是软件层，主要因为针对GPU的优化，GPU有CUDA语言以及很多的Library

- 例子： [torchsparse]()

- 部署实例：如TensorRT，后端是平台代码(CUDA)，且包含了计算图以及编译的优化。

### 3.2: DSA(Domain-specific Architecture)设计

# Surveys

### [ACM TRETS 19] A Survey of FPGA-Based Neural Network Inference Accelerator （我们的DPU架构）

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424100631.png)

- Hardware Optimization
    - Computation Unit Design： 低比特的计算单元，或者Conv的变体
    - Loop Unrolling strategy： 目的： increase parallelism & save computational waste
        - Compiler的优化在这个层面？或者是这个层面的一个generalized？（TVM/MLIR）
        - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424102020.png)
        - Loop Tiling是Loop Unrolling的一种更高层次的,Loop tile将unrolled好的数据给排布好成一个个的tile。loop tile的size可以调整计算存储比
        - Loop interchange决定了tile的计算order
            - `之前我对这几个概念的认知是有错误的：Loop unroll指的是将一个大循环拆成若干个小循环并行做（本质上是堆更多的硬件计算单元以获得更高的并行度），这个拆解率N，一般都是在compiler优化之前决定的，它决定了：有多少个硬件单元会同时并行执行，也就是决定了**并行度**`
            - `Loop Tiling则是另外的一个维度，决定了每个周期（流水线中）进多少数据，可以控制计算存储比。属于Compiler的优化空间，另外Loop exchange描述的才是这个四层循环的先后顺序，也可以被compiler所优化，这些都包含在compiler优化中的scheduling部分中`
            - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424221353.png)
    - Data Transfer Design & On-chip memory design
        - systolic array is an example
        - data reuse for high parallelism: 
            - too much broadcast: high fan-out & high routing time: lower the working frequency
            - 在这里需要获得一个 locality & parallelism之间的trade-off？
    - Cross-layer Scheduling：
        - fuse many layers together
        - 一般不做
    - 我们在compiler中提到的scheduling？和这些组件的关系？ （`参考上面有关loop unrolling的讨论`）
        - `compiler优化的时候会考虑到数据的依赖关系，如果有了复杂的数据依赖优化不好的话，就会导致存储带宽不足`
        - `数据依赖所描述的其实是：一个数在被拿出去计算还没写入的时候，下一次又需要读它，所以需要等这种情况。然而访存的locality问题，其实更general，只要是时间和空间分布不具有局部性，都会导致访存locality较差`
        - `数据依赖性描述的是DataFlowGraph和data数据之间的依赖关系； 时间局部性是同一个数据被多次使用，空间局部性则是相邻数据存储之间的关系`
    - 以及cache的locality？
        - cache的locality描述的其实本质是一个对memory的读取速度和data pattern有关的问题。
        - 能够反映到数据带宽(bandwidth)，sequential且规则的DDR access的访问带宽，肯定比random access更快*
        - `一个on-the-side的点，cache是一个主动式的存取，而bufffer是被动式的，cache会主动接受处理器的需求拿到想要的数据，而buffer是要从里面自己拿的，所以DPU中其实只有buffer。`


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
        - systolic array面对的是memory-bounded的任务，由于computing不再是瓶颈，脉动阵列尽可能在一次memory读取中执行更多的运算，来balance memory&compute
        - 与传统的单PE结构不同，Systolic array中有多个PE，数据流经过PE1处理完之后又进入下一个PE，`在较小的内存带宽的情况下获得较高的内存吞吐率`
        - `优化memory读取的方法？`
            - Tiling & Data Reuse，尽量减少片外memory的读取次数
            - 增加On-Chip Memory以及片上buffer的大小
    - EdgeTPU： ASIC for edge devices, 针对的平台是Raspberry-Pie，Pixel Smart phone

- NVLink： 更快的接口能和片外memory更好的交互：
    - 对标PCIe

### A few other stuff

- [AI框架的演进趋势和MindSpore的构想](https://zhuanlan.zhihu.com/p/225392622)
    - AI芯片的发展对AI框架的发展：1） 优化与硬件耦合度提升，计算图层面的优化已经收敛，需要和算子层进行联合优化。2）异构的编程
    - AI编译器将成为重点： 1）静态图动态图统一的JIT能力，如JAX； 2）面向芯片以及硬件平台优化：XLA，TVM  3）AI编译的基础设施 MLIR
    - AI的JIT：动态shape容易搞定，但是动态type就难了，动态图和静态图无缝的转换困难
    - 编译加速： 基于Pattern的算子融合和基于模板template的算子生成两条路。（算子融合更难，是否能让模板template-free是一个promising的方向）
    - 基础设置： MLIR的理念先进，希望通过Dialect来支持各种编译器。

- [深度学习加速器的整套流程](https://zhuanlan.zhihu.com/p/107091687)
    - 包括以下几个环节： 
        1. 算法层加速：剪枝量化等，主要是Python
        2. 编译器层面：将深度学习框架生成的计算图 -> 算子和计算流程(包括了Scheduling)（Python/C++）
        3. 软件交叉验证： cModel（软件验证编译器的输出结果）
        4. 指令集合的设计： 与硬件结构设计紧相关： RTL
    - RTL层面的设计：
        - 一些中间操作(Act/BN)等虽然计算量不大，但是会对整体加速形成瓶颈，需要让他与卷积之间形成流水
        - 指令的设计：包含了数据的存储位置，需要的数量，计算的方式
    - 加速器架构主要包括的部分：
        - 外部总线：与外部DDR交互读数
        - 内部存储Cache：存储所用到的参数和数据，需要提供给计算core，cache缓存是一个缓存区以避免DDR的带宽瓶颈
        - 指令解析：
        - 内部总线：每个计算核的数据通路
        - 计算Core
        - Interconnect：不同核之间的互联
    - 验证主要包括两大部分： 1） cmodel以验证编译器输出指令的有效性   2）RTL仿真结果是否正确
        - 典型例子如UVM： 生成随机指令给cmodel，随机参数写入DDR
    - 驱动：目标：完成DDR的初始化，线程控制的配置以及中断响应。
        - 将权重写到DDR中，然后通过AXI总线配置FPGA寄存器。
        - 对Zynq平台的标准操作： 1）制作RTL工程，生成bit和hardware配置； 2）利用SDK生成fsbl，对zynq器件的一些硬件配置； 3）制作linux系统的uboot，kernel，deviceTree等；制作打包成一个boot.bin制作SD卡镜像并烧录到SD卡。
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220424205009.png)


- [陈天奇： 深度学习编译技术的现状和未来](https://zhuanlan.zhihu.com/p/65452090)
    1. Polyhedral Model

- [深度学习编译器有哪些有价值的研究方向和参考文献？： TVMCon的主题报告](https://www.zhihu.com/question/338039895/answer/2278797498)
    - 深度学习的几种抽象层次：
        1. 计算图 Computational Graph：将计算图表示成DAG，可以进行算子的fuse，改写，并行等操作。（Relay，XLA，TorchMLIR，ONNX都在一个级别）
        2. Tensor程序表示 Tensor Program： 对子图的loop优化，对于DSA设计还要包含内存搬运的优化 （TVM，MLIR）
        3. 算子库和运行库 Library & Runtime： 类似cuDNN，cuSparse等
        4. 硬件指令 Hardware Primitive： 专用硬件的硬件张量指令 （LLVM）
    - 目前的生态会在各个层面单独做Multi-stage Lowering，然后把问题丢给下一个层级继续优化


- [Adi Fuchs的加速器系列文章](https://medium.com/@adi.fu7/ai-accelerators-part-i-intro-822c2cdb4ca4)
- [Part2 处理器演变](https://zhuanlan.zhihu.com/p/465787742)
- [Part3 架构基础](https://www.jiqizhixin.com/articles/2022-02-13-2)
- [Part4 The rich landscape](https://medium.com/@adi.fu7/ai-accelerators-part-iv-the-very-rich-landscape-17481be80917)
- [Part5 Final thoughts](https://medium.com/@adi.fu7/ai-accelerators-part-v-final-thoughts-94eae9dbfafb)
    - (没有特别get到他的体系和逻辑走向…但是提供了很多strong evidence)
    - AI加速器的基本架构类型： ISA（指令集加速器），典型例子Intel的X86，ARM，MIPS，RISC-V（与CISC对比），一般由算术指令，内存操作，控制操作
    - Systolic Array：（目前主要的AI加速引擎都采用了该种方式，比如Tensor Core）
        - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220425101354.png)
    - 数据流操作Dataflow，程序被表达成数据流图：DGF（Dataflow Graph）
    - 内存管理：内存访问能耗比较多，比计算高处几个数量级，可以用近存的方式(Near-memory Computing,更激进的方案就是PIM)


### TODO

- more tvm 
- read digest of DAC/Sysarch papers