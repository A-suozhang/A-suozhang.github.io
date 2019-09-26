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



# Python
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