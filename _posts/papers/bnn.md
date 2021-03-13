

## [LatentWeights Do Not Exist: Rethinking Binarized Neural Network Optimization](http://arxiv.org/abs/1906.02107)

##  🔑 Key:         

* 主要Stress了主流BNN的paper当中的利用全精度的weight的梯度STE到binary weight的时候的不合理之处
  * STE有着比较强的连续性的假设，在高比特下仍然适用，但是到了低比特特别是二值化的时候就不再能够适用了

##  🎓 Source: 

* PlumerAI & HKUST
* [https://github.com/plumerai/rethinking-bnn-optimization](https://github.com/plumerai/rethinking-bnn-optimization)

##  🌱 Motivation: 

* 用一个简单实验说明了STE所得到的pseudo grad不好使的原因： 用训练完之后的高精度weight去做inference，发现效果很差

##  💊 Methodology: 

* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210218195201.png)
  * 本质上还是用到了比较错误的梯度，通过对其做滑动平均，将其作为动量

##  📐 Exps:

* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210218195608.png)

##  💡 Ideas:  

* 认为Latent weigt的方向和绝对值大小有着不同的涵义，绝对值大小其实是类似于Momentum或者inertia(惯性)的量



## [High-Capacity Expert Binary Networks]()

##  🔑 Key:         

1. Model Capacity: conditional computing(类似Dynamic Path)
   * 训练N个expert来应对多种不同的data portion(?); 用一个简单的gate函数选择一个expert来做inference
2. Representation Capacity
   * 研究了width vs depth
   * increasing "effective width" under the same budget
3. Network Design
   * search for the optimal direction for scaling-up the binary network

##  🎓 Source: 

##  🌱 Motivation: 

1. Expert Convolution：
   * 对同样的input，有a set of weights
   * 用了一个Gating，对输入首先做一个aggregation(这里用的是一个linear的，直接把所有输入乘起来，再乘一个可以学习的projection matrix，再reshape成一个N维的),之后apply Gating func其函数为WTA(Winner takes it all)即为找最大的。
     * 有点意味是将输入数据分为N个part，每组参数负责一个子空间？
2. Model Size
   * 做了Depth与Width的讨论，显然增加width更好，但是增加width会导致K^2的模型大小变化，所以额外使用一个K^2的group size来保证complexity不变
3. Network Design
   * Semi-Automated(基本就是实验扫一遍，然后找)
   * Block Arrangement：每个stage block的个数变化所造成的结果变化不大（因为每个stage widthx2了但是spatial/2了，所以complexity等价，加groups获得了明显更强的模型，最后两个satge用16/32效果最好）

##  💊 Methodology: 

##  📐 Exps:

1. follow 2-stage pipeline: 1) 训练bianry activation，全精度weight的网络 2) 以此为初始化，进行都binary的训练
   * 实际训练的时候是先FP的训练一个expert，然后复制N份；再进行本来的第一步

##  💡 Ideas:  





