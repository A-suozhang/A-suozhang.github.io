---
layout:     post                    # 使用的布局（不需要改）
title:      Salad代码阅读?!          # 标题 
subtitle:   这是什么专业框架啊(战术后仰)...   #副标题
date:       2019-11-2           # 时间
author:     tianchen                      # 作者
header-img:  img/bg-cat1.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - DL
---

> 在找源码的时候发现了Salad这么一个库,处理的是和我一样的问题,其中有一部分东西可以拿过来用,但是层层封装,真的很难看懂

# Salad 源码阅读

* Digitlader源码
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191103103654.png)
  * 在其Augment arg中输入{'mnist':2}
    * 'mnist'是其self.augment的key,对每个单元,部署*augment_function*
    * 该augmentation function 默认为Augmentation,第二个输入参数是*augment[key]*也就是value的值,这里是2,对应着下面
    * Augmentation又是一个类,来自salad/datasets/transforms, augmentation类的init里有一个n_samples,对应的就是上面的2
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191103104213.png)
      * 首先该类别在init的时候就~~经历了一个ImageAugmentation函数~~
        * 并不是这样,而是构建了一个self.transformer是一个ImageAugmentatoin对象
        * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191103104551.png)
        * 最后Augmentation返回的其实应该是一个list
          * 里面这句话 ```outp = [torch.from_numpy(x).float() for x in X] + [y, ]```
          * 本身一个tensor是iterbale的,沿着dim=0去iter,最后+相当于append上y
          * 这也是为什么我们如果调用```DigitsLoader```,在mnits集上,会得到一个image1, image2, label的list

* augmentation
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191103104551.png)
  * 默认值如上
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191103112237.png)

* Net
  * Conditional BN
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191103111913.png)
* 定义出的net会有一些conditional param
  * 在定义的时候,需要确定n_domains
  * 在前向的时候,需要确定d=?

* 定义了一个自己的_batch_norm
  * 因此BN相关的一些参数并不在 state_dict当中也不会被更新
    * 这也就是我那个多卡的bug产生的来源 - 因为不在state dict中的BN的running mean参数并没有在net init的时候被DataParallel复制到多卡上
  * 同时放弃了BN的afine trasnform,其实严格来说是不好的