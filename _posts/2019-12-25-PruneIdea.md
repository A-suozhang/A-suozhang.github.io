
# Prune Ideas

* Main Higlight: 用TaskLoss来知道PruneRatio的改变
	* A. 能处理带Budget的剪枝问题(和实际场景更接近)
	* B. 能**直接**学习到层间的SaprsityAttributeTradeOff
* Advantages
	* 能做到B的一般是相对黑盒的方法,用基于regularization的方法/bayesian的方法去学习一个Mask(ClfLoss和regularization(SparsityLoss)都直接指导了Mask的学习(梯度回传),都没有显示的PruneRatio
		* 但是此类方法都不能做到对Budget可控
	* 能做到A的一般都带PruneRatio,调整PruneRatio的依据一般都较为间接(通过交替更新)
		* ECC-交替更新Weight和PruneRatio,PruneRatio更新的梯度只来自于一个对最终功耗的线性模型 EnergeyConsumption(SparsityRatio)
		* AMC-黑盒的从RLAgent采样,不可控,可能会不满足
		* AutoCompress - 用RandomPertubation以及SA(模拟退火)的Heuristic方法指导PruneRatio的调整
		* BAR-没有用PruneRatio,而是在Loss上乘上了一个包含Budget的分段函数(平滑后)
* Workflow
* Problems
* Fracs
	* 可以对ResBlock的PrimalComponent选取方式做一个AblationStudy

## MainContribution

* ⭐Loss Guide Prune Ratio
  * AutomativeIterativeFlow的采用Acc来指导
  * 在End2End并没有指导TaskLoss没有指导
    * 他们都是往下的 (BAR不是，但是它是细粒度剪枝)
      * Compared to BAR
  * Variational的其实有Loss指导的
  * Why？
    * 从Budget出发，需要调整PruneRatio，希望其能被Loss指导
    * AiF的用局部搜索的方式，敏感度分析不准确耗时
      * Differentiable的比搜索的找层间Tradeoff要快

* Feature
  * ChannelPrune（Structured Prunning）
  * UnderBudget (Task)
  * Probabilistic (SoftLabel)
  * GraphAnalysis

* Depthwise压缩

* Workflow
  * 两次软化
  * 控制Variance
  * 函数族设计


## Category

### 调整PruneRatio

* 搜索类的调整PruneRatios
  * IterativeFlow
  * Acc-Guide
  * Example: AMC
* Loss做TradeOff但是不能显示的控制，并没有显示的PruneRatio （End2End）
  * End2End
  * Loss-Guide (as a surrogate)
  * Example: ECC(?)
* PruneRatio根据显式控制
  * End2End
  * Loss-Guide (as a surrogate)

### 依据

* Norm（Weight空间）
  * GroupLasso之类，之后可以按照
* Bayesian
  * 根据Loss（不仅有ClassLoss）来后验 （BudgetSpace）， *不跟据L1Norm来选择*
  * AutoPrune
  * VI
* 其他（加SaliencyModule）

## Budget

  * 是否支持Budget

## Hard / Soft

* Recovaranle
* Probablistic一定是Recovarable
* **ICLR2020**有一篇说明Revocable的，用到了STE的思想

## Shortcut

* 不清楚	
* MorphNet / GroupLasso / Ours
* 细粒度处理（BAR）

## Experimental

### Glocal Setting
> CompressionRatio x2/4/6/8

* GroupLasso
* 每次迭代按照梯度*L1Norm
* MorphNet
* WN / VQ

---

* AMC (摘抄其他文章)
* Adam-ADMM

### Network & DataSet

* Cifar10
  * VGG16
  * Res18
  * MobileNetV1
* Cifar100(可能可以凑)
* ImageNet
  * VGG16
  * Res50
  * MobileNet V2

* **相关的内容整理一下能用的**

* **看一下汪总上次发的文章**


## Timeline

1. Baseline开发
   * Morphnet  (周三Eve)
   * WM / VQ
2. Plan1开发
3. ADMM开发 （周四）
   
* 周五开始调实验，写文章

* Cifar10 
* ImageNet

## Problems

* L1Norm来作为选择标准有一些Arbitary
  * 可以用Grad*L1（也算是一点缓和把）
* 为什么需要一个PruneRatio？
  * 和我们完成同样的任务，可以用BAR这种方式
    * Bayesian可以用Saliency这种替代，做的都是学一种选择标准
    * 最后用Loss拉高，或者是ADMM的方式来直接限定Budget
* 利用ADMM本身是否可以就看作找到了PruneRatio和Weight的一个权衡
  * 而不用像我们这样拉
  * Bayesian+ADMM - 太松
  * T-NAS