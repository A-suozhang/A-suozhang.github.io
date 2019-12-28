---
layout:     post                    # 使用的布局（不需要改）
title:      PruneIdea   # 标题 
subtitle:   prunning... 
date:       2019-12-25           # 时间
author:     tianchen                      # 作者
header-img:  img/11_30/bg-ucentre.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 碎片知识
---


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
	* 
* Problems


* Fracs
	* 可以对ResBlock的PrimalComponent选取方式做一个AblationStudy
