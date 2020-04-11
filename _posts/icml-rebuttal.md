



### Reviewer 1
1. Problems
   * Missing - 与这篇文章所类似的参考文献的Discussion 
   * Readability Low
   * Experiment缺少reproduction detail
     * Table 2/3 少内容
     * 且 Table 4 的4-4-4 与Table2 不concide
   * 希望将重点体现在一处，这样更好理解(最好对整个流程加一个Pseudo的Flow)
   * 对MantissaBit具体是怎么做的阐述的不是很明确
   * 还希望有GroupScaling对Addition across layer(Shortcut)以及Addition across sample(BN)
   * 希望对不同的L的选取对Quantization Error的影响有一个可视化的处理,另外还对"Extremely Distributed　Value"不确定
   * 只说了Double-Precision的Weight,少了Activation
     * 还有对Can We still use the same Hardware Element不清楚
   * 觉得应该把Discussion的一部分内容加到Experiment当中
   * Res18 on CIFAR-10
   * 实验内容缺少与其他工作的网络对比
2. Attitude
   * Could Raise Score if get fixed
3. Ideas
   * Stochastic Rounding - 导致了Double Precision
   * Potential Much，if see hardware design
4. TYPO 



### Reviewer 2

1. Problems
  * 缺少泛化性的分析/Code for Reproducing/Hardware Perf. Modelling
  * specific quantitative configurations seem arbitray
  * Ablation Study 少了Stochastic Rounding
  * Treatment of Overflow
  * 也说了应该把Discussion里面的内容放到Method里
  * Double-Precision-Deployment可能被误认为是64...
  * ImgNet只有Res18显得有点不够
  * Table4对L的选取是否Generalize
    * 换L的硬件代价
  * 觉得Computation cost的分析不够
  * No Model of Memory Efficiency

### Reviewer 3

1. Problems
   * Table3的Res18 有不同的baseline Acc
   * Figure8/9中Display Number
   * 觉得Mantissa Method是为了Efficiency提出的,在Table5中显示它们还可以提升性能 



---

* 读一下这两篇文章Flex和Gryos,它们是怎么分析的,对于DataFormat
* Scaling Factor的分法是直接分的Channel
  * 而不是依据统计分布来决定的