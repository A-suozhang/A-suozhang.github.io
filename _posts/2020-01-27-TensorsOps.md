---
layout:     post                    # 使用的布局（不需要改）
title:      Tensor 操作速查           # 标题 
subtitle:   提键盘忘操作        #副标题
date:       2020-01-27            # 时间
author:     tianchen                      # 作者
header-img:  img/1_28/nantong-0.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 常用
---

# PyTorch Tensor Ops

### [Tensor](https://pytorch.org/docs/stable/tensors.html)

* 直接赋值其实两个变量是指向同一个内存地址的
  * 需要两个一样值的，用 Variable(x.data.clone())
  * clone是**深拷贝**,而一般的赋值传的是**引用**
* 而copy和"="的区别其实在Python中
  * 前者是建立新对象,后者是传一个引用
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191207191020.png)

#### 尺度变化

* **想要使用尺度变化,首先要清楚计算的Broadcast原理**
  * *本质,想象为一个先拓展到和另外一个一样大,再做逐点乘*
  * 成立条件: 每个对应维度大小要么相同,要么其中有**一个是1**,要么其中有一个不存在
  * 如果x,y的维度不同,会优先给空着的维度补0 ```[3,2,4,4]*[2,4,1]``` 
    * 注意在这里,最低维y为1,x为4,可以,但是如果y的最低维为2就不行
  * 对于每个维度.计算结果是xy中较大的那个
* 尽量避免使用Permute操作,比较费时间,如果是为了做broadcast还是使用更加直接的方式
  * 一个比较好的例子: BN的mean是[c]但是输入参数是[N,c,w,h]我们需要将mean broadcast过去
    * 将mean reshape为[1,c,1,1],再做加减法 ```mean.reshape([1,-1,1,1])```
    * 另外一种方式是将其拓展为 [c,1,1] ,然后再做 ```mean[:,None,None]```
      * **这种做法必须向后对齐,很神奇**
* 当我想制作一个fake data的时候 ```[Batch, Channel, W, H]```
  * 拓展数据,使用```tensor.expand([1,1,3,3])``` 
  * 或者是使用```torch.stack([x,x,x], dim = 0)```
    * stack会直接新建一个维度,而cat则不会
    * 比如```[1,2,2]```做一个```torch.stack([x,x], dim = 2)``` 
      * stack ```[1,2,2,2]```
      * cat  ```[1,2,4]```
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191207200443.png)
* 对于一个正常的Tensor,需要做转置的时候用```x.T```,但是如果这个tensor只有1维度,比如```torch.tensor([1.,2.,3.])```,对其做T得到的shape和原来一样
	* 这时候就可以用```x[:,None]```
* 对于想要在For-loop中将一些Tensor给concat起来
	* 可以先利用一个List将需要concat的存放,然后用```torch.stack(list, dim=0)```进行拼接
	* 生成这个list可以用list comprehension来做
* 在我的代码里使用inplace的方法```tensor.mul_()```和使用赋值的方法差距很大,原因不是那么知道!
	* inplace 和 ```x = ...```
	* ! Inplace的替换和赋值有区别,比如说都是对bn.weight赋值,用第二种方式相当于将bn层的weight指向了一个新的tensor,但是我们一开始加到optimizer中的还是原来那个Tensor,导致了新的参数没有被更新
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200102224419.png)
	* **注意inplace修改和替代的区别!这个坑经常会遇到!**
* 对于一个(300,)的tenso，其实本质上是一维的，第二维度可以是任意值，和(300,1)有本质的区别 



# Numpy Ops

* Cheat Sheet
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211145511.jpg)
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200217174234.png)
