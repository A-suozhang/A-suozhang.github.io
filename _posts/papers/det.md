> this is a review, also some new stuff about the Detection Task

## Review of some Detection





* Faster RCNN Flow

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210225234312.png)

* 部分：
  1. Feature Extractor： 输出Anchors以及对应的feature representation
  2. RPN：输入anchor的feature，输出Proposal(相当于filter以及regress之后的anchors)
     * 有两个Loss，L_cls是判断fore/background; L_reg是和GT box的regression(Smooth L1 Loss)
     * 其中coords是学习的offset(偏移量以及系数)而不是实际值
  3. ROI Pooling: 
     * 输入的是依据Proposal所在feature map上抠出来的部分
     * 目的是让不同大小的Proposal的大小变得一致
  4. Detection Head
     1. 两个loss： L_cls与L_loc
  5. NMS则是最后的postprocess，只针对head之后的输出做(将一些很类似的都是正确的框去除掉)



* Yolo / SSD最后的head是一个全卷积的结构，是将Loc和classification fuse在一起的
  * 但是RetinaNet(虽然也是一个One-Stage的方法)是有两个Head的



* Weakly Supervised
  * [一个知乎解读](https://zhuanlan.zhihu.com/p/313717328)

---





### [Learning From Noisy Anchors for One-Stage Object Detection]()

##  🔑 Key:         

* 对Anchor box进行基于IOU的nms选择(判定是Positive/Negative)的时候，是一个hard split -> binary labels, 会造成noise
  * 有一些Anchor的IOU虽然很低，但是包含了很多有意义的语义信息
  * 本文针对这个我问题**引入了一个Score(Anchor-wise的)，一个Cleanness Score**，作为soft label，以及sample re-weight factor(更cleand的anchor被以更大的权重被consider); 被用来决定当前anchor的importance in training； 含义是probability of anchors to be successfully regressed to desired locations and classifie
    into object (or background) labels. 
* 有一个primitive是outputs  from net can indicate the noise levels； 所以combination of localization acc(regression subnet), prediction score(classification subnet)
  * 来源于在一些Noisy Label的研究中, confidence score of network一定程度上代表了noisy level of samples（比较noisy的label的prediction会不是很confident）

##  🎓 Source: 

* Salesforce & Univ. of Maryland

##  🌱 Motivation: 

##  💊 Methodology: 

* explicitly consider the label noise
* 一些类似于来自weakly-labeles social media的场景，采用re-weight去更加care clean的label set
* 这种Score可以认为是一种soft label，去防止over-confident prediction

---

* the cleanness score: = \alpha*(Acc_regress) + (1-\alpha)Conf_class （只针对pos的anchor，neg的置0）
  * 可以被认为是Soft label，作者stree了由于Acc_regress也被融入了soft label，让classification branch也接受到了regression的相关信息
* 在做reweighting的时候，经过了一个1/(x-1)的函数去enforce large variance,整个外面在加上一个\gamma次方
  * 这个系数r在cls与regress loss中都是针对每个box加上的

##  📐 Exps:

* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210225211541.png)

![image-20210225211548454](C:\Users\A-suozhang\AppData\Roaming\Typora\typora-user-images\image-20210225211548454.png)

* 该方法的结果produce了更加milder的box

##  💡 Ideas:  

* One-Stage的更需要Reweighting(更imbalance)
  * 因为大量的neg anchor(proposal)
  * focal loss更关注一些hard and noisy sample
* 朴素的One-Stage的detector flow
  * 一个feature extractor为每个Anchor生成feature vector
  * 然后经过两个branch(regression/classification)分别输出label prediction(CE Loss)以及regress coordinates(Smooth L1 Loss)
    * 由于每个Anchor的Label是没有的，所以需要人为定义，定义为于GT Box的IOU超过某个threshold就是前景的某个Class否则就是bg；(这里存在一个hard label assignment不太合理)
* 更新Regression branch的时候比较直接，但是更新Classification branch的时候，只有一小部分与GT相同的boxes才会被认为是positive sample；而后一些诸如Focal Loss之类的Hard Negative Mining的方法，用来mitigate data imbalance

* [一个知乎的解读](https://zhuanlan.zhihu.com/p/97811344)

* 这个方法虽然做了smoothing，但是只是对positive box做confidence；



* 对于同一个Anchor匹配了多个GT box，class的标签是如何assign的？如果只是以IOU最大的类作为one-hot的label，那语义的指导应该有问题？（20%的人，80%的马）



### [Noise-Aware Fully Webly Supervised Object Detection]()

> 主要是提出了一个所谓的Webly-Supervised的比较新颖的概念，Flow上我们不能参考，可能可以参考一些Insight

##  🔑 Key:         

##  🎓 Source: 

##  🌱 Motivation: 

##  💊 Methodology: 

##  📐 Exps:

##  💡 Ideas:  



### [Towards Noise-resistant Object Detection with Noisy Annotations]()

##  🔑 Key:         

##  🎓 Source: 

##  🌱 Motivation: 

##  💊 Methodology: 

##  📐 Exps:

##  💡 Ideas: 



### [Training Object Detectors With Noisy Data]()

* Combine Co-training in the det domain
* 讲了一个提高automatically labelled dataset(可以认为是和GT有noise的label set)上性能的故事，这样就不用标数据了
* 背景故事是Co-Teaching
  * 针对的是noisy label的场景，Insight是网络很容易learn好clean label所对应的部分，而noisy label所对应的部分一般样本的loss也会比较大(以这个为标准去做样本筛选)
  * 有两个一样的网络，他们的weight不一样；用A网络做Inference，选择一个batch中一定比例的loss小的数据来作为B网络的用于训练的数据(且vice versa)
    * 可以认为是提取出了比较clean的label去优化其他网络，这个比例从0逐渐增大

