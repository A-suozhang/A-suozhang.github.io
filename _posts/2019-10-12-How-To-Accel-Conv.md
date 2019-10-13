---
layout:     post                    # 使用的布局（不需要改）
title:      卷积是如何加速的？         # 标题 
subtitle:   How 2 Accelerate Conv     #副标题
date:       2019-10-12            # 时间
author:     tianchen                      # 作者
header-img:  img/10_1/bg-xueyuanroad0.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - CNN
     - System
---

# 如何加速卷积？

> 参考自[知乎文章-如何实现高效卷积](https://zhuanlan.zhihu.com/p/85344625)

> 翻译自[Texas Univ的一篇论文](https://www.cs.utexas.edu/~flame/pubs/GotoTOMS_revision.pdf)

* 最愚蠢的Numpy For Loop Conv(6层For循环，毫无并行)
    * 没有包含Dilation和Stride
``` python

'''
Convolve `input` with `kernel` to generate `output`    
input.shape = [input_channels, input_height, input_width]    
kernel.shape = [num_filters, input_channels, kernel_height, kernel_width]    
output.shape = [num_filters, output_height, output_width]
'''
for filter in 0..num_filters    
    for channel in 0..input_channels        
        for out_h in 0..output_height            
            for out_w in 0..output_width                
                for k_h in 0..kernel_height    
                   for k_w in 0..kernel_width   
                    output[filter, channel, out_h, out_h] +=   
                    kernel[filter, channel, k_h, k_w] *    
                    input[channel, out_h + k_h, out_w + k_w]
```
* 用这种方式做MobileNet的第一层卷积，需要22s左右，经编译器优化可以到2.2s，但是Caffe中只要18ms
    * 在CPU中，很多时候卡到的是**内存访问的时间，For-Loop让内存访问非常频繁，无法充分利用缓存Cache，而且CPU的缓存本来就很弱**

---
* **性能/运算速度**的指标是吞吐量(throughput),单位就是Flops(每秒的浮点运算数目)

> 假设CPU： 1. 2 Core 2. 每个核主频2.5GHz(每s执行2.5*10^9个指令周期) 3. 每个机器指令周期可以处理32Flops(AVx+FMA，这里面包含了SIMD)，对应的峰值性能就是**160GFlops**

---
* 一般的4维张量都是被拉成一条存在内存里的
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191012194858.png)
* 可以用Halide语言嵌入到C语言当中
    * 其能够分开**数据**和**调度策略**
    ``` c
    Halide::Buffer C, A, B;
    Halide::Var x, y;
    C(x,y) += A(k, x) *= B(y, k);  // loop bounds, dims, etc. are taken care of automatically
    ```
---
* 矩阵乘法(Matmul)或者是GEMM(Generalized Matrix Multiplication)
    * 有Blas，Eigen等矩阵乘法库来处理这些问题
* 卷积将**二维滤波器/图像块**展开为**矩阵**，这个操作叫im2col   
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191012195241.png)
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191012195308.png)

* GEMM+IM2Col就是CPU上对卷积加速的标准操作

---
* **Loop Reordering**
    * CPU操作对应的RAM表示的是内存(访问速度慢)更快的一层是CPU的Cache，CPU会*从内存中加载需要的数据和相近的数据到缓存中*
    * 一旦缓存中的数据不满足我们的需要(发生了缓存缺失*loop miss*)，就需要再一次*访问内存*就慢了
    * 所以我们要**改变loop的形式(loop reordering)**来最大化的利用缓存，计算顺序要和缓存顺序对应上
* **Tiling 平铺**
    * 对于比较大的矩阵乘法，缓存里面存不下一行数据，需要反复的存和读同样的数据，叫缓存颠簸(cache thrashing)
    * 将大矩阵相乘转化为小矩阵相乘(就像铺地砖一样)
* **SIMD (Single Instruction Multiple Data)单指令多数据流**
    * 在多个值上执行相同的指令(加法或者乘法)
* **FMA(Fused Mult-Add)**
    * 某些处理器支持将乘加作位一个指令周期
* **多线程Threading**
    * 多核的时候可以多线程，复制同样操作并行
* **Loop Unrolling**
    * 写软件代码的时候手动拆开重复写循环(?)