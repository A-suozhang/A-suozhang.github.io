---
layout:     post                    # 使用的布局（不需要改）
title:      LPCV20比赛准备 / Object Detection简述              # 标题 
subtitle:   看看这一年目标检测领域又背着我发展了多少(雾)        #副标题
date:       2020-05-07             # 时间
author:     tianchen                      # 作者
header-img:  img/bg-neon.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - DL
    - 综述
---

# 比赛TODO

> 需要做的东西还真不少

* 目前主要需要做的是去完成、Pytorch - TF - Tflite一套工具链
    1. 编译安装tf与bazel
    2. tflite的使用
    3. 模型转换工具
* 还有定点上不知道有没有什么空间

* TODO：
    1. 编译tf  (Finished)
    2. 安装android studio
        * 编译apk
    3. 跑起来EfficientDet
        * COCO Dataset  
    4. 正常走完android开发的块速流程



# Tflite工具链的安装流程

> 充满血与泪的一周多，tf这东西真不是人用的，主要内容填写在[Git issue](https://github.com/A-suozhang/my-debug-record/issues/11)当中

* 主线是[OVIC Benchmarker for LPCV 2020](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/lite/java/ovic)


0. PIP安装TF
    1.1 [tf-2.1版本在import的时候出现问题](https://github.com/tensorflow/tensorflow/issues/35968)
    1.2 Downgrade 2 tensorflow 2.0 via ```pip install tensorflow-gpu==2.0.0```

* 其中的prequisite部分指引向

1. 从头开始编译TF
    * 由于TFlite需要build from source
    * 主要参考了[tf官方的版本](https://www.tensorflow.org/install/source)
    * 主要遇到的阻力是网络问题
        * 由于遇到了需要下墙外的资源的问题，由于bazel需要通过建立localhost，所以直接用proxychains搭配ss不能正常安装
        * 首先给git配置代理 ```git config --global http.proxy socks5://127.0.0.1:10808```
            * 然而到了之后这个还是不起作用，反而需要改动
            * (另外一种改变git config的方法是在```~/.gitconfig```)
        * 我们实际需要的是http代理，而socks并不能提供该种代理，所以使用了privoxy
            * [Privoxy的配置教程](https://docs.lvrui.io/2016/12/12/Linux%E4%B8%AD%E4%BD%BF%E7%94%A8ShadowSocks-Privoxy%E4%BB%A3%E7%90%86/)
            * 首先```sudo apt-get install privoxy```
            * 编辑 ```sudo vim /etc/privoxy/config```
                * 搜索```listen-address```保证```listen-address 127.0.0.1:8118```存在，将来的http代理将在8118端口
                * 将``` #forward-socks5t / 127.0.0.1:1080 .```接触注释(注意结尾的“.”)
            * 重启Privoxy
                * ```systemctl restart privoxy```
                * ```systemctl enable privoxy```
            * 在```~/.bashrc```中添加
                * ```export http_proxy=http://127.0.0.1:8118```
                * ```export https_proxy=https://127.0.0.1:8118```
        * 用```curl www.google.com``` 来测试是否成功
        * 注意改完之后git的config也要改过来，```git config --global https.proxy https://127.0.0.1:8118```
    1.1 开始安装一些初步的 
        * ```sudo apt install python-dev python-pip```
        * ```pip install -U --user pip six numpy wheel setuptools mock 'future>=0.17.1'```
        * ```pip install -U --user keras_applications --no-deps```
        * ```pip install -U --user keras_preprocessing --no-deps```
    1.2 安装bazel
        * 查看需要的bazel版本，查看tf源码目录下的```configure.py```
        * 在[Release界面直接下载.sh](https://docs.bazel.build/versions/master/install-ubuntu.html)
        * 运行```xxx.sh --user```
        * 在bashrc中添加```export PATH="$PATH:$HOME/bin"```
    1.3 编译Source Code
        * ```git clone https://github.com/tensorflow/tensorflow.git``` 
        * ```git checkout r1.15```
        * ```./configure```
            * 请完成sec2.1再调用此行
            * 这里实际上需要葱figure好android再执行
        * ```bazel build --config=v1 --config=opt //tensorflow/tools/pip_package:build_pip_package```
        * (希望确实是能够work的)
    * ```./bazel-bin/tensorflow/tools/pip_package/build_pip_package /tmp/tensorflow_pkg```
    * ```pip install /tmp/tensorflow_pkg/tensorflow-version-tags.whl```


* 我不知道是否需要手动安装，但是我现在的jdk版本是```11.0.7```，应该可以直接apt安装获得

2. [配置android](https://www.tensorflow.org/lite/guide/android)
    * [下载ndk](https://developer.android.com/ndk/downloads/older_releases.html#ndk-17c-downloads)
    * [通过android studio安装sdk]()
    * 在1.3的```./configure```中指定SDK/NDK Path
    * 讲道理下面的指令就可以编译了
        * ```bazel build -c opt --fat_apk_cpu=x86,x86_64,arm64-v8a,armeabi-v7a \ --host_crosstool_top=@bazel_tools//tools/cpp:toolchain \  //tensorflow/lite/java:tensorflow-lite```
    * 将在```bazel-bin/tensorflow/lite/java/```生成了aar文件，将其复制到android项目中并修改```build.gradle```
    * (其实这一步我真的不清楚到底如何起效果，而且看上去好像是删除了两行android依赖才完成的最后的编译，所以成功性存疑)

3. Follow [Ovic Guide](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/lite/java/ovic) 然后测试一系列东西
    * 从一开始的validate，到生成最后的apk
        * ```curl -L https://storage.googleapis.com/download.tensorflow.org/data/ovic_2019_04_30.zip -o /tmp/ovic.zip```
            * 用来测试的```.lite```文件可以在这个文件夹中获取
        * 如果要利用自己的模型，需要从tf的ckpt开始
            1. 利用4步骤中完成的export_ssd_graph来从ckpt文件中获取frozen_graph.pb文件
            2. 利用tf编译产生的bazel-bin中的tflite-convert从上一步的pb文件中生成.lite文件用作提交与测试
    

* TFLite
    * [Example Kerasflow+Lite](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/lite/micro/examples/hello_world/create_sine_model.ipynb)
    * 分为
        * interpreter - specially optimized for certain device platform
            * device-optimzied kernel
            * pre-fused activation/bias
        * converter - convert the tf model for interpreter
    * [Get Started](https://www.tensorflow.org/lite/guide/get_started)
    * [Converter只支持以下op](https://www.tensorflow.org/lite/guide/ops_compatibility)
    * 4 Type
        * Post-Training Dynamic Range quantize
            * Float Scale
        * Post-Training Integer Quatize
        * Post-Training float 16 
* [OVIC-guide](https://github.com/tensorflow/tensorflow/tree/master/tensorflow/lite/java/ovic)
* [Install and build tflite](https://www.tensorflow.org/lite/guide/android)





# Object Detection 综述

* 从去年这个时候做SSD-FPGA之后，就没怎么关注过这一块了，在DL中一年不关注一个领域的发展可见一斑（特别还是Detection这么火的一个领域），来补课了
* 文章来自于[Arxiv](https://arxiv.org/abs/1905.05055)又看的[zhihu](https://zhuanlan.zhihu.com/p/72838705)的翻译版，我自己再夹杂一些私货，算是二次咀嚼了，~~反刍（不是)~~

## Metrics
* Precison = True Positive/All Positve 
    * 最后判断为Positive的有多少是正确的
    * *放心度* 模型判断出来正确我用起来多放心，可以很放心，但是过度谨慎（recall度 ）
* Recall = True Positive/(TP+FN)->All Positive(GT)  - 
    * GT中有Positive多少被判断出来了
    * *敏锐度* 模型能够把客观正确的判断出来多少，可以很敏锐，但是很粗心(Precision低)
* AP - Average Precision
    * 不同Recall下的Precision的Mean
    * 详细计算方法参考[这个教程](https://zhuanlan.zhihu.com/p/72838705)
        * 分别计算PR(这里计算的是框子的数目,不是类别)并画出PR曲线
        * PR曲线下的积分面积就是AP
* mAp - meanAP - AP是基于某一类的，mAp就是求AP关于类的平均

## Methods

### Faster-RCNN 2015

* [参考解读文章](https://zhuanlan.zhihu.com/p/64817300)
* ~~祖宗~~
* CNN end2end训练，速度几乎实时
    * 一次性完成区域提取，特征提取bounding box和回归
* 用RPN(Region Proposal Network)以“Anchor”的概念，代替了Selective Search
    * 以Feature Map（经过降采样之后不大的）上的每一个点作为中心，在**原图**映射为9个anchor(3个长宽比，3个尺度)
    ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910094153.png)
    * 这里RPN就是分别对Feature Map的每一个像素点的256维度的特征，做1x1x18的卷积得到每个AnchorBox对应的前景后景置信度(9x2)以及每个BBox的位置xywh(9x4)  (讲道理每个锚点对应会原图都会有一个特定位置，RPN给出的Proposal Box也应该在那附近 - *?怎么实现这一点*或者说是这个所谓的anchor映射到原图，并不是一个直接的位置映射(FeatureMap左上角对应到原图的左上角)或者说，Anchor到原图的映射，正是从每个Anchor中训练出来的xywh这个过程，所以所谓的“锚点”其实是抽象的点)
    ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910094246.png)
    * 以IOU作为前景后景的打分标准
* 经过RPN，我们将原图中产生的anchor box返回到Feature Map，得到了一些Proposal，然后做一个ROI Pooling（将抠出来的Proposal转化为统一大小）
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910095516.png)
    * 比如这里将7*5的Proposal做MaxPooling到2x2
* Loss是分类损失以及位置回归误差之和
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909152753.png)

### R-FCN 2016

* *Full Conv + Position Sensitive*
* 全卷积，所有参数在整张图片上是共享的
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910103115.png)
* Faster-RNN在ROI-Pooling之前是严格卷积，具备平移不变性，但是之后就不存在了
    * RFCN采用Position Sensitive Score Map    
* Faster-RCNN的RPN之后的部分需要独立地对每一个Region做
    * RFCN想要尽量共享参数
    * **提取出Roi这一步是一样的，但是Faster-RCNN对每个Roi(Region Proposal)独立处理了，但是RFCN不一样**
* 细节：
    1. 首先经过卷积层，最后一个FeatureMap有三个分支
        1.1 RPN的操作获取Region
        1.2 获取Position-Sensitive-Score-Map (KxKx(C+1))的用来分类
        1.2 获取Position-Sensitive-Score-Map (KxKx(4))的用来定位
    2. 对Position-Sensitive-Score-Map做Roi-Pooling(Average Pooling)
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910103318.png)
* ？How Position Senstive
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190910103424.png)
* 即使Roi偏了，也能正常识别

###  Yolo（You Only Look Once） 2015

* **简单暴力**所有东西拿CNN硬干
* 第一个1-Stage的网络，抛弃了区域提取+验证的2-stage思想，直接将原图分为S*S的grid cell,每个里面塞B个Bounding Box,推测C类，每个BBox预测5个value（xywh+conf）最后需要预测的Tensor为 ```S*S*(B*5+C)```
* *How 2 Get Box* 每一个BBox都会对应一个confidence，等价于GT与该Bbox的IOU（如果这个Grid Cell里面没有Obj则conf=0，判断方式是gtbox的中心是否落在这个gridcell里面）然后Bbox的conf再和20个类别所得的置信度相乘，获得每一类的最终置信度，并经过一个NMS（防止一个Obj对应着太多的Box）
* Loss = 定位误差 + IOU误差 + 分类误差
* 速度极快，精度也随着改进跟上了
    * 引入Res，引入Anchor，加入BN
* 发展历史：
    * Yolo v1：
        * Features： 1. 用Grid Cell划分区域之后，对每个区域独立进行检测（分治法） 2. 完全End2End 3.应用了Leaky relu
        * Shortcomings： 1. 位置准确度不足 2，对小物体效果差（由于每个格子最多检测出一个物体） 3. Recall比2-Stage的方法小
    * Yolo V2 （Yolo9000） - 主要改善Recall以及定位精度 ~~各种Trick拉满~~
        * 加入了BatchNorm
            * 🤔Why Could BN Help: 网络层数加深，整体数据的分布会越来越偏，由BN强制拉到一个比较标准的分布（不然会发生Internal Covariance Shift）
                * 本质上还是把数据拉到损失函数比较敏感（梯度比较大的地方）便于收敛
        * Anchor Box的引入
            * RPN的核心也是Anchor，通过anchor，让feature map上的点映射回到原图的点，这样需要预测的就不是直接的坐标而是偏移量了
            * Yolo V2把全连接换成了Anchor Box方式进行预测
        * 用K-means训练Bbox的Size
        * 添加了细粒度模块添加了一个PassThrough层，将相邻的特征按通道叠加而不是按空间叠加，这样让高低分辨率的特征能够串接起来
            * [这篇文章]()表示这种方法和Res异曲同工，我没理解
            * 🤔:Why Res Help(我感觉这个观点有点意思) Resnet通过一个shortcut feedback，学习的是f(x)-x，*举一个比较极端的例子*，假设f(x)=x（恒等映射）学习一个g(x) = f(x)-x=0比学习一个x要简单（而且NN的初始化一般都是0均值的）
        * MultiScale-Training 多尺度训练
    * Yolo V3 （Even Faster）
        * Deeper
        * ResNet Backbone

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909152711.png)


### SSD (Single Shot Detector)

* 引入了多目标检测
    1. 多尺度特征图(大的特征图检测小物体（分辨率更高）) - 借鉴了金字塔结构
    2. 摈弃FC，用卷积作为来检测
    3. 保留了先验框（Faster RCNN中的**Anchor**）为每一个单元格子都设置长宽比不同的先验框，是BBox的基准，对于每个anchor box都独立作出判别
    4. 训练中Ground Truth与Box的配准： yolo较为naive，gt的中心在那个单元格，就找（该grid cell中）与其IOU最大的bbox负责
        * 而SSD则更为“谨慎”，对于每一个gt都找出图中所有的anchor box（比前面少了当前的grid cell中）与其IOU最大的，然而这样会造成负样本过多（超多的anchorbox没有对应的gt），因而需要降低标准，对每一个anchor，如果与gt的iou大于某一阈值，也看做匹配

### FPN（Feature Pyramid） CVPR 2017

* 提出了一种新的特征提取器(在Object Detection中属于BackBone)
* 金字塔的结构，更好适应多尺度,Pyramid体现在特征图的尺度,分为几个级别(每个级别是一个ResBlock)
* 解决了之前的图像金字塔的*计算量过大的问题*
    * 原本的比较Naive，相当于是在多个Scale下分别进行预测
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190924124258.png)
* **核心思想** **高低层特征融合**把高层的语义传下来,同时获得具有*高分辨率与高语义度的特征图*,可以有利于小目标的检测
* **如何能够结合高低尺度分辨率的特征?** 把更抽象,语义信息更强的高层特征图(在更深的位置)把该层的特征横向链接(lateral connection)到前一级特征图,让高级特征得到增强(两个feature map尺度要相同-via*对更深层的feature map做上采样(via直接复制,不是插值或者是反卷积)*,才可以利用位置信息)
  * 浅层的feature map需要用1*1Conv把channel数目也弄得和深度的一样
  * 结合的方式是直接**逐像素相加**

---

* NN中融合特征的方法
* 直接逐像素Add相当于先Concat再让通道共享同一个卷积核-用Add其实更节省计算量和参数
* **ADD Example**
    * ResNet的Skip Connection
    * FPN中的多尺度Feature Map融合
* **Concat**
    * Skip Connection其实很多时候用的都是concat - *DenseNet*

---


* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190924101053.png)
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190924124837.png)

### RetinaNet
* 注重于*one-stage为何不如2-stage精确度高的问题*
    * 前景后景之间的不平衡 （背景框太多让训练跑偏）
    * 样本难以区分程度差距大 （需要花更多精力在难区分样本上）
* 通过**Focal Loss**解决

### TridenNet （~~在深海使用三叉戟~~）2019
* 关注*尺度变化的问题，研究不同感受野的优劣*
* 将传统conv换*空洞卷积*，从而改变不同分支的感受野的大小

## 🤔🤔🤔Thoughts
### Rethink Anchor
* anchor 的本意是“锚“(anchor box - 锚框)表示固定的参考框
* B4anchor: 采用金字塔尺度+SlidingBox的方法去判断这个位置可不可能有对象 *笨重* 
> 这个尺度，的某个位置处，有没有我们想要的目标
* 而anchor：首先预设置一组不同大小的框(几乎覆盖了所有位置和尺度)每个框负责与它IOU最大的对象  
> 这个固定的框子(Anchor Box)内部有没有我们想要的对象且偏离多远
* 🤔：两者一个基于对象，一个基于框。
    * 个人觉得两者在思路上还是类似，Anchor所做的主要是通过对卷积之后特征图上设置“锚点”并以此为基点(通过卷积)将Proposal映射到原图上的点这一步，省去了原先SlidingBox每个Box都相当于要做特征提取(卷积)那样又慢又没有考虑到全图的信息
    * Anchor Box相比于之前的方法主要 1，减少了计算量 2.解决了长宽比
* Anchor's Problem:
    * Anchor的尺度和大小如何确定()
        * YoloV2聚类，训练出anchor
    * 小目标是一个难题
* **Region-Based Methods** 3 Stepss
    1. 一个Backbone给出Feature Map
    2. RPN之类的网络出Region Proposal(必须对每个Region独立，参数不共享 - *体现在RPN对FeatureMap每个输出点做1x1xN的卷积*)
    2.5. Roi Pooling
    1. 最后的分类回归网络

### Anchor Free方法
* Anchor的缺陷
    * 需要手动支持
    * Anchor的匹配让极端尺度的判别很难
    * Anchor的数量很多的时候需要一个不平衡的问题
        * Focal Loss能一定程度上解决
* 一开始的Yolo和DenseBox，目前的SOTA是CVPR2018的CornerNet
    * 2019年，很多基于Keypoint的Anchor Free方法逐渐被提出
        1. CornerNet
        2. CentreNet
        3. ExtremeNet


## SubFileds (~~术业有专攻~~)
### 多尺度目标检测 MultiResolution
* Timeline
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909183225.png)
* 


## Refs
* [知乎专栏](https://zhuanlan.zhihu.com/p/79254792)

