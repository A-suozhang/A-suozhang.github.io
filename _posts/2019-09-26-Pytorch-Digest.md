---
layout:     post                    # 使用的布局（不需要改）
title:      PyTorch踩坑笔记           # 标题 
subtitle:   以及Python的一些些坑爹特性        #副标题
date:       2019-09-26              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-nmb-corner.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - Coding
---

# PyTorch
* [跟着官方学下net应该咋子写](https://github.com/pytorch/vision/blob/master/torchvision/models/resnet.py#L38)
    * 继承```nn.Module```的模块成Block类，init的时候一般需要调用父类的构造函数```super(BasicBlock,self).__init__()```


# Python
* 我居然又记不住格式控制输出了？！
    * ```print("this is %d" % 20)```
    * ```print("%d---%d"%(10,20))```
    * **Format()** - 用“{}”来代替“%”
        * {}里面带数字可以替换顺序 ```print("{0}>{1}".format(1,2))```
        * {}里面“:x”可以进行进制转换
## OOP 
* 一个类的```__init__(self, )```是构造函数
* **类的方法第一个参数要填self！**
* ```self.```相当于this指针
* Python没有New关键字，直接使用类的名称构造对象，相当于调用构造函数```p1=Person(18,0)```
* 内置了一些类的属性
    * ```__dict__``` 包含了一个类的属性所表示的dict
    * ```__name__``` 
    * ```__module__```类定义所在的模块
* 约定俗称的规律```__foo```代表的是private变量
* 而super方法则是通过*调用父类方法来显示调用父类*(可以避免需要用父类的名字来调用父类，需要将self传进去)
    * ```super(BasicBlock, self).__init__()```