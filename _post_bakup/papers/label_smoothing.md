

# [Regularization via Structural Label Smoothing]()

##  🔑 Key:        

* 提出了一种设计Label Smoothing的方法，并且通过推导BER(Bayesian Error Rate)来决定最佳的策略

##  🎓 Source: 

* AISTATS2020

##  🌱 Motivation: 

##  💊 Methodology: 

* 依据Data以BER为准则改LS的alpha

##  📐 Exps:

##  💡 Ideas:  





# [Does Label Smoothing Mitigate Label Noise?]()

##  🔑 Key:        

* proposed Label Smearing(a more general case of label smoothing)
* 说明了label smoothing能够改善在noisy label下的classification任务，说明了其与本身noisy label情境下常用的forward/backward correction方法是相关的

##  🎓 Source: 



##  🌱 Motivation: 

##  💊 Methodology: 

* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210216205049.png)

##  📐 Exps:

##  💡 Ideas:  

* 调整的是label smoothing的smoothing factor(\alpha)也就是



# [Label Confusion Learning to Enhance Text Classification Models](http://arxiv.org/abs/2012.04987)

##  🔑 Key:         

* Instances may related to multi-labels
* one-hot, over confidence, model overfitting
* confused/noisy datasets

##  🎓 Source: 

* Shufe

##  🌱 Motivation: 

##  💊 Methodology: 

* LCM(Label Confusion Model)： 
  * discover the semantic overlap among labels
  * label distribution changes for different instances with same label
* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210210112038.png)
* 设计一个label的embedding network，与data的embedding计算KL散度
  * 输出以一个\alpha比例加上one-hot再softmax作为最后的softlabel

##  📐 Exps:

* 在mnist上做的实验…可以说是基本完全没用了*

  ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210210113052.png)

##  💡 Ideas:  



----

# Det + KD

## [Learning Efﬁcient Detector with Semi-supervised Adaptive Distillation]()

##  🔑 Key:         

* 针对One-stage的detector
* 针对两种难样本(hard sample)去利用teacher指导student学习
  * hard-to-learn: teacher with low certainty
  * hard-to-mimic: teacher & student differes greatly
* 只对分类的Focal Loss进行了Distillation

##  🎓 Source: 

* Sensetime

##  🌱 Motivation: 

* 用到了SemiSupervised的Distillation flow

##  💊 Methodology: 

 ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210218210255.png)

##  📐 Exps:

* Teacher backbone res101; student res50

  ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210218210323.png)

##  💡 Ideas:  



## [Learning Efficient Object Detection Models with Knowledge Distillation]()

##  🔑 Key:        

* Hint Learning
  * adaptive layers of feature learning
  * 对齐intermediate的feature representation
* KD: 
  * weighted CE for solving the class imbalance
  * teacher-bounded loss

##  🎓 Source: 

* NIPS2017

##  🌱 Motivation: 

##  💊 Methodology: 

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210218211617.png)

1. Weighted CE for Class Imbalance:
   * 是一个one-hard的Label的hard loss
   * 以及利用teacher logits的soft loss的加权
   * 由于在classification中只存在foreground之间的误分类，而det中很多的是foreground和background之间的误分(只是一个固定define的对于background更大的权重1.5/1)
2. KD Regression Loss
   * 可能会是非常error的guidance
   * 认为它是一个student的upper bound，如果teacher的错误比student还大，就不用了
   * [?] - 既然FPN并不是公用的，如何保证输出的anchor是一样的呢？(Hint是否在某种意义上是在对齐？)
3. Hint Learning
   * 以L1 Loss对齐intermediate activation，中间插一个adaptation layer(用的是1x1 conv)，发现了即使是channel size一样这个层也很关键
   * 如果resolution对不上，用了一个简单的padding trick

##  📐 Exps:

##  💡 Ideas:  



## [LabelEnc: A New Intermediate Supervision Method for Object Detection](http://arxiv.org/abs/2009.14416)
##  🔑 Key:       

* 找到一种intermediate的representation来enforce det task的learning(通过对Label进行encoding)
* 用一个AutoEncoder学习到autoencoding label的方法； 然后再用这个label representation，用一个auxiliary loss来训练backbone

##  🎓 Source: 

##  🌱 Motivation: 

* insight来自于bakcbone的weight的pretrain并不是det-aware的，所以可能suboptimal
  * 由于det head中的weight是随机初始化的，所以这一支的grad很noisy，甚至会对backbone的训练degradation(比如有的时候训练head的时候需要freeze住backbone的参数，就是为了避免这种情况)

##  💊 Methodology: 

* LabelEnc： 

  * label encoding function应该是去approximate optimal的det head的逆
  * 求逆困难： 用一个Label空间的AutoEncoder去建模NN的逆(就是det head，由于高度非线性，所以很难获取)
    * 训练loss是reconstruction loss以及det loss
  * 对于如何找到optimal的head，一起alternative的训练存在recursive的依赖，采用了一个“unrolling”
    * 有点像是Bi-Level的formulate?在appendix中有推导
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210219104759.png)

  ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210219104226.png)

  ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210219104247.png)

##  📐 Exps:

##  💡 Ideas:  

* Joint training of auto-encoder很关键：
  * 在训练label encoding的时候也需要det backbone，不能只用reconstruction



## [ONE SIZE DOESN’T FIT ALL: ADAPTIVE LABEL SMOOTHING]([2009.06432.pdf (arxiv/org)](https://arxiv.org/pdf/2009.06432.pdf))

##  🔑 Key:         

* 根据GT的bbox在整个图片中的占比，调整distillation的比例

##  🎓 Source: 

##  🌱 Motivation: 

##  💊 Methodology: 

##  📐 Exps:

##  💡 Ideas:  





## [MimicDet: Bridging the Gap Between One-Stage and Two-Stage Object Detection](http://arxiv.org/abs/2009.11528)

##  🔑 Key:         

* 在one-stage以及two-stage之间插一个transform layer去让anchor对齐，能够distillation

##  🎓 Source: 

##  🌱 Motivation: 

##  💊 Methodology: 

##  📐 Exps:

##  💡 Ideas:  



# [Distilling Object Detectors with Fine-grained Feature Imitation](http://arxiv.org/abs/1906.03609)


##  🔑 Key:         

##  🎓 Source: 

##  🌱 Motivation: 

##  💊 Methodology: 

##  📐 Exps:

##  💡 Ideas:  