## [Learning Imbalanced Datasets with Label-Distribution-Aware Margin Loss](http://arxiv.org/abs/1906.07413)

##  🔑 Key:     

##      

##  🎓 Source: 

* Stanford MaTengyu

##  🌱 Motivation: 

* Margin-based: encouraging a large margin as the regularizer

##  💊 Methodology: 

* data dependent / label dependetn regularizer
* 对per class寻找其uniform label error bound, 推bound为每种Class设计了一种Bound
  * ![image-20210224222627127](C:\Users\A-suozhang\AppData\Roaming\Typora\typora-user-images\image-20210224222627127.png)

##  📐 Exps:

##  💡 Ideas:  

![](https://github.com/A-suoz

hang/MyPicBed/raw/master//img/20210224221141.png)

* existing soft margin loss, encourage the minority classes to have larger margins
* 其他面向Imbalance任务的方法
  * Re-weight： Over-sample the minority classes
    * some even sample-level weight
    * empirically importance weighting does not have a significant effect when no regularization is applied
  * Re-sample
  * Margin-Loss: "max-min" - Hinge Loss
    * many uses class independent margin(this paper uses class-aware margin)
  * View it as "Label shift" in transfer learning


# [High-Performance Large-Scale Image Recognition Without Normalization](https://arxiv.org/abs/2102.06171)

##  🔑 Key:         

* Boosting  CNN perf. without the batchnorm layer (NF-ResNets - batchnorm-free resnets)

##  🎓 Source: 

* Deepmind

##  🌱 Motivation: 

* no BN: could not handle big LR / strong data aug

##  💊 Methodology: 

* AGC (Adapative Gradient Clipping)
* design a family of Normalize-Free ResNets

##  📐 Exps:

* 实现方式是先在一个300M的大dataset上进行pretrain再在imgnet上进行finetune
* AGC的insight是对grad clip的threshold应该依据weight本身的大小来决定：
  * 而采用element-wise的比layer-wise的要好
  * 可以看作是一种relaxation of normalized optimizers(LARS)



##  💡 Ideas:  

* Revisiting BatchNorm
  	1. 减少了residue(shortcut) branch上的activation的scale
   	2. 解决了mean-shift的问题(越深越严重)
   	3. 本身可以做为regularizer
   	4. 能够使用更大的LR，增加了训练效率(fewer steps of param update）



## [GLOM: How to represent part-whole hierarchiesin a neural network](http://arxiv.org/abs/2102.12627)

💡 Ideas:  

* 重点关注的问题是如何在NN中建模”部分/整体的关系“

  * 比如说对于一张图片，不同的元素出现在不同的位置颠倒一下（人脸的例子），其实是不能处理的
  * 比较合理的方式是将各个part以一种parse tree的形式建模起来(感觉像一个graph)；但是由于NN不能动态的分配一部分资源去构成这个”parse tree“： - Capsule本身是解决这种问题的

* 希望设计的模型GLOM

  * 包含了很多column，他们的weight一样
  * 每个column包含了 a stack of autoencoder
    * 每个autoencoder transformer the feature at some level to 上一个或者下一个level through a bottom-up encoder/top-down decoder
    * 这些level表示了特征层次： 比如鼻头-鼻子-脸-人；
    * 同一个level的多个column是为了构建出一个”island“
      * proposes the idea of using islands of similar vectors to represent the parse tree of an image

  ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210304170657.png)

  * 某个level的embedding受4个方面影响：
    1. bottom-up
    2. top-down
    3. embedding at previous time step
    4. attention-weighted average at the same level from nearby column
  * Capsule的思想是：
    * 为每个可能出现的objectie/part额外set aside一系列neuron，use a routing algo去dynamically connect small set of active capsules,构建一个graph