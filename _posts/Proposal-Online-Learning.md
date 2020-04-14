# Proposal

## Story

* 我们所构想的大概故事是：
  * 在端(Edge)上基于Pretrained Backbone进行Finetune，去Adapt到一个更小的子任务(Subtask)
  * A. 以达成Scene/User Customization   (提升子任务上的精度)
  * B. 以及Computational Effort Reduction (类似于Online Pruning)

* 对于一个已经在大数据集上Pretrained过的Backbone Model(如ImageNet Pretrained的Res50)，在新的Sub-Task/Domain上进行Finetune，以做到适应。
  * 1. SubTask - 可能是原本大任务的一个任务子集 (1000 Class -> 50 Class)
  * 2. Domain - 比如User-Customization，比如笔迹/语音识别的任务，将不同用户认为是不同的Domain，通过tuning让模型更加适合某用户的同时对模型本身进行精简。

## 算法上可能的方向

> 单纯的做Finetune可能会感觉Contribution相对不足，所以算法上可能有以下两大方向进行探索，如果能做到以下两点中的一点就有一定意义

1. 考虑到实际场景中可能无标注数据多，可否从少量监督信息(Less-Supervised)？
  * **目的：减少Tuning过程本身的监督信息**
  * 且已有与训练过的Backbone，是否可以利用其获得Pseudo-Label
  * 或是引用Unsupervised/Self-Supervised领域的一些技巧来协助进行无(弱)监督的Finetuning
2. 如何去在Finetune的同时精简模型(Online-Prunning)？
  * **目的：获得一个更高效的模型，以加速推断**
  * 比如：类似[LCCL](https://arxiv.org/abs/1703.08651)去训练出一个稀疏的Mask，实际前向时可以跳过被Mask掉的内容

* Others： 如何使Finetuning本身高效？
  * *目的：让Finetune的本身变得高效*
  * 比如只Tune特定层等


## 硬件上可能的方向

> 同理，对应不同的算法优化方向，硬件也有对应的改进可做

1. 从Less-Supervised出发
   1.1. 新的Finetune方法对定点(Fixed-Point Training)会不会有特殊的可以优化的点？
     * 梯度的分布是否会有一些特性可以指导定点方法的改进？
     * 比如说对BatchNorm参数的特殊定点化处理？
   1.2. 对Finetune训练进行硬件的训练考虑?
     * 若算法上不是朴素的训练(比如引入了一些额外的模块)，需要进行额外的硬件设计
2. 从Online Prunning出发
   2.1 如果进行的是Kernel Pruning(更加细粒度的Prunning)，涉及稀疏的训练
     * 参考之前工作《Compressed CNN Training with FPGA Accelerator》进行拓展，稀疏矩阵运算加速以及Load Balance的处理
   2.2 如果进行的是Channel Prunning
     * 从Mask中保留部分的Channel加入运算，对现有的DPU结构，是一个非绑定的取数逻辑，需要加入动态将不同Channel的参数Load到对应Bank的指令


## Example

* [《An Optimized Design Technique of Low-bit Neural Network Training for Personalization on IoT Dev》]()
  * DAC19 KAIST
  * 目的是做在线的Finetune，设计了两个场景(并不是Popular的公认数据集)
    * ImageNet -> Rainy ImageNet
    * 笔迹识别
  * 是有监督，没有精简模型或者是对无监督数据进行探索

* [《Towards Lower Bits Multiplication for Convolutional Neural Network Training》]()
  * 低比特训练的算法工作

* [《Compressed CNN Training with FPGA Accelerator》]()
  * 稀疏训练硬件加速器的工作