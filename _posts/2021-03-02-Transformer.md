layout:     post                    # 使用的布局（不需要改）
title:      Sth. About Transformer            # 标题 
subtitle:  热度热度… 
date:       2021-03-02            # 时间
author:     tianchen                      # 作者
header-img:  img/2020/street3.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签

     - survey

# Transformers

> 由于Transformer在各个领域都十分火热，终于想起来要蹭这个热度

## Basics

### Attention & Self-Attention

* Attention: 对于一个Seq x_i ，有一个element-wise的w_i加权处理之后作为y_i
* Self-Attention：本身并没有参数，直接是W = X*X^T，用来描述某个seq本身的一些相关性，哪里比较重要
* Transformer中的拓展Self-Attention引入了一系列参数
  * Key，Query, Value - 思想是让Key和Query的点乘dot product
    * (相当于element wise的乘法最后加起来,等价于nn.Linear())(自相关)能够拟合出Value； 
    * （感觉像是嵌入了这样的一个surrogate task让他去学习到更好的Self-Embedding形式?）
  * 计算流程:

```
# 'input' x_i [num_token, dim]
# 'Key' K_i = W_{Key} [4,4] @ x_I -> [num_token, dim]
# 'Query'
# 'Value
# Score = Softmax(Query*Key) - [num_token, num_token]
# Output - [num_token, dim]
```

* 尺度变化：

  * 原本比如说输入是[4,3]
  * 对应[4，4]的W，点积猴得到[4,3]的key/query
  * key query做点积之后得到[3,3]的score，在dim=-1做Softmax： 这一步的物理意义是，对于3个input的的分别有一个归一化的[3,1]的查询，表示更倾向于去query 3个value中的哪一个
  * 每个score会加权上所有token所对应的embedding并且sum起来
  * sum(dim=0) -> [3,3]

  ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210302152758.png)

  ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210302152913.png)

* Multi-head的Self-Attention
  * 认为W_key/W_valuet/W_query都是xN份，将结果Concat起来
  * 如果input也同样divide成n份的化，总体计算量并没有变化(有点类似于GroupConv的感觉)

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210302155258.png)

* Positional Encoding： 一般会有一个额外的positional encoding的layer来capture序列信息

* Tokenize: 将输入数据转化为一个个token(在NLP的任务中是一个单词)

* Transformer Block：
  * self-attention layer
  * noramalization
  * feed forward
  * normalization layer
* Overall Arch
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210302154632.png)

### Positional Encoding

- [一篇解析文章](https://mp.weixin.qq.com/s/QlR528MYCioEuYwJIs20Pg)

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/23299697e7c8ee5b5b07d4c2b1323b7.png)

## RoadPath

1. Transformer - 一开始在NMT任务中被propose，去描述global dependency

2. BERT -> GPT(超大规模pretrain)

   * 由于Transformer massively parallel，所以能够在超大数据集上进行pretrain

   * 使用了两种surrogate task做unsupervised pretrain： 

     ​	1) masked language modeliing

     ​	2) Next sentence prediction

## Visual

* 最早是用来替代last stage of convs, 将一些pixel按照semantic来group起来作为token
* iGPT：self-supervised Pretraiing of visual Transformer
* ViT: 是一个pure transformer，applied on seq of image patches

## Efficient

1. Pruning:
   * head数目的redundancy(width)： 发现不同层需要的head，有些甚至只需要一个
   * use intermediate center node to change the fully-connected attention into sparse ones
   * depth：layer-wise droping
   * lottery+transformer： 找到了其中存在lottery
2. Matrix Decomposition：
3. KD:
   * use dot product between values(self-attn outputs) as a form of KD
   * learnable transformation to map different feature into the same space
4. Quantization
   * embedding the input into high-d binary vectors
   * k-means 4-bit quantization
   * Fully-Quantzied 8-bit Transformer

5. Compact NN Design

   * (dot product between representations from different input tokens in a given sequence)
     
* simplify the self-attn to the span-based conv (就是最后的atten-map对tokens加权求和的时候只选取一部分的)
   * sparse graph 
   * Local Sensitive Hashing: (Reformer)

## Ideas 

* 在CV任务当中，Transformer更多的被作为一个feature selector(通常是从一些patch或者是pre-acquired feature作为token，来寻找他们之间的关联)，因为它本身更注重于比较远的数据之间联系的mining
* Transformer相比于RNN更加efficient，因为其linear层和self-attention层可以parallel起来



## Mateirals

- [一个非常育婴的基础教学](https://github.com/BSlience/transformer-all-in-one)

