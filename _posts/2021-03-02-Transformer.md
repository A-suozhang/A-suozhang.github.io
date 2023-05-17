---
layout:     post                    # 使用的布局（不需要改）
title:      Sth. About Transformer            # 标题 
subtitle:  热度热度… 
date:       2021-03-02            # 时间
author:     tianchen                      # 作者
header-img: img/diffusion/disco-12.png #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签

     - survey
---

# Transformers

> 由于Transformer在各个领域都十分火热，终于想起来要蹭这个热度

## Basics

> https://www.youtube.com/watch?v=TrdevFK_am4

* take in a set of points(tokens), do pair-wise inner product(has N^2 connections - quadratic attention)
  * quadratic too big - local-attention
    * VIT: glocal attention to patches(更加粗粒度的基本单元)
* Transformer's params w(maybe the attention map) is not fixed, 是on the fly计算的
  * 主要是对于输入的位置不是固定的，所以是order invariant的，需要positional encoding
* Vit的本质就是将一个16x16的patch转化为一个transformer能够理解的单元：
  * 就是256长度的一个seq乘上了一个learnable embedding
* Different with Conv & MLP
  * conv(image, neighbourhood mechanism) & mlp(could fit any function) has strong inductive prior
  * conv could be viewed as the inductive bias(我们已知一个完备的模型能够拟合任何函数(MLP)但是我们由于数据不足，不足以完全的刻画func，所以我们只需要bias to其中一个部分，让其有某种性质(Conv)
  * Transformer是一种比MLP更加general的方法
    * 但是现在的shortcut其实也是一种inductive bias
  * 由于其非常general，非常inductive，所以在数据量激增的现在能够更好的表现

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

* 本质上就是：

  * 对每个time-step的输入token；都算出一个key，value(feature_dim可以自定义，由input token乘一个weight matrix得到,但是这两者需要一样)，然后做dot product，对所有的token做softmax，就可以获得attention map；之后的结果乘上Value(也是由input token经过一个weight matrix得到的)，就是最后的输出。
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210313220040.png)
  
* 尺度变化：

  * 原本比如说输入是[4(num_token),3 token_feature]
  * 对应[token_features，key_feature]的W，点积猴得到[num_token,key_feature]的key/query
  * key query做点积之后得到[num_feature ,num_token]的score，在dim=-1做Softmax： 这一步的物理意义是，对于3个input的的分别有一个归一化的[3,1]的查询，表示更倾向于去query 3个value中的哪一个
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

### [VIT的代码实现](https://github.com/lucidrains/vit-pytorch)

### ViT

* 就是直接把图片分成pacth，每个patch认为是一个word，整张图片是一个16x16的sentence，直接学习这些patch之间的联系。
* Standart Learnable 1-d pos encoding
* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210528204014.png)

### TNT: Tranformer in Transformer: 

* 内部用pixel embedding通过一个inner transformer块的得到的结果再和Patch embedding相加起来作为outer transformer块的输出

* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210330134139.png)

### SwinTransformer：Shifted Window, Hierarchical Transformer

* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210330134613.png)

* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210330141314.png)
  * Patch-Merging： 由于输出的时候每个patch会对应一个dim=C的feature，需要downsample的时候，将2x2的patch给concat起来过一个linear(4c-in 2c-out )
  * 选取了4x4作为初始的patch大小，一开始的输入尺度就是W/4xH/4xC, window是在patch的上面的,一般用7x7 window：(224=7x4x8)
* window-based的MSA，window的数目会随着网络的加深而变细，同时channel数目增加； quadratic to num-token -> linear to num-token
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210330140247.png)
* 对比原本是直接一个patch中全部做global attn，传统的改用window-based的W-MSA，虽然可以减少计算量，但是每个window都是independent的，缺少了cross-window的information；这里用sliding window，每隔一个transformer块，会将window平移M/2个块,突出体现(Neighboring Non-Overlapping)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210330140413.png)
* 用了UperNet的Framework来做Seg(应该是替换了PPM)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210330152602.png)

### DeiT: [[2012.12877\] Training data-efficient image transformers & distillation through attention (arxiv.org)](https://arxiv.org/abs/2012.12877)

* 原本的ViT需要很大数据集的pretrain才能获得好的效果，这里只需要在正常imgnet上训练
* 一篇偏Technique report的文章

### Aggregating Nested Transformers

1. 不是和其他的Vision TR一样使用一个Hierarchy的结构，而是用Local-TR on non-overlapping blocks, then aggregate them hierarchically

   * 如何选取aggregation func就非常关键了

2. 做到了**Converge Faster & Less Training Data **

3. 直观看起来这个flow很像pyramid

   * 文中表示了和Pyramid的区别是:
     * pyramid一般都是用的global SA + spatial ds：(local attention which is efficient)
   * 作者表示对于 **如何建模long-range的information communication**对translation invariance关键，其他方法用了一些比较sophisticated的方法来做到这种
     * 而作者认为它通过将Block aggregation映射回到原图来做，所以nearby blocks可以很方便的沟通信息(与Swin当中的Window overlap异曲同工)
   * 有一个部分专门讨论了如何aggregation
     * 是否在SA内部进行query subsample(在len_seq的维度上)： 在data-efficient数据集上问题很大(reso直接和seq len有联系)
     * 如何使用Conv （小size）
     * which pooling   (Maxpool比别人好很多)
     * 是否是unblocking关键

   * 实际aggregation的过程中由于有一个Unblock和conv，所以映射到原图时候这个aggregation中其实涉及到了information communication among blocks

     ![image-20210528211003775](C:\Users\A-suozhang\AppData\Roaming\Typora\typora-user-images\image-20210528211003775.png)

* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210528210710.png)

### PVT
* 类CNN Macro的TR，堆叠多个TR-block，channel数目逐渐增加，spatial逐渐减小


### DeepViT：Retention

* 提出了ViT不能过于Deep，到了深层attention的map比较similar了

* 做了一个参考实验，加大Attention的embedding dim，确实一定程度上解决了这个问题，所以证明了**Attention Collapse确实是Bottleneck所在**
* Retention: **Interaction between AttnHeads**
  * 发现了同一个TRBlock的不同head之间attnmap很类似
    * 它们本来应该focus on different aspects of input token
  * 去找Cross-head Communication去regenerate map
    * ？本身引入multihead的意义是对一个token的feature有更rich的解读，但是现在它学不出来了，所以不把它们隔离开来，而是融合成一个head
    * 引入了一个learnable的Transofmation，在head-dim上aggregate attn-map，做norm之后再乘上V
    * 说自己是effective & easy2implement

### Twin：
* focus在Spatial Attention Mechanism的设计
* 一个问题是CPVT有Swin好嘛？(ck文章里有没有对比)
1. Pos Encoding related
	* 提到了PVT的结构，对于dense prediction的任务很重要，作者表示surprsing SwinTR更好
	* 发现了PVT的核心问题在于使用了继承自ViT的absoulte pose encoding，因为这样就没有translation invariance了
	* 使用了CPVT中所提出的CondPosEnc，在每个stage的第一个encoder block中用PosEncGen利用最朴素的CondPosEnc产生方式: 2-d Depthwise Conv
2. Attention Scheme related: Spatially Separable Self Attention
	* 第一个层面: Locally grouped: 分2-d图片为小的patch，每个patch只是local的做attn(Swin的做法，问题是没有inter-patch的information communication)； receptive fieled从NxN变成了KxK，文中表示这样做掉了很多点 O(k1k2HWd)
	* 第二个Global SubSampled Attention，去做cross-group info exchange，本质上就是在window维度做attn。O(HW^2xd/k1k2) 
	* 两者结合起来就像Depthwise-Pointwise一样

### CondPosEnc：
* 文章里没有和Swin比，可能是出现前后
* 本质上就是一个DeothwiseConv on input-Feature Map
	* condition体现在它的计算是condition在它的local neighbor下的，(也涉及了一个从seq revert回2d然后再apply conv的这样一个过程)
	* Permutation variant but translation invariant
	* having the aibility to express the absolute to some extent（作者通过实验表示这点很重要）
* 整理了Pos_enc的Main scheme
	1. absolute: Sinsoido / Learnable
	2. relative: retlative 2-d >> 2-d Sinsoido 
	3. Dynamic: 
	4. this paper's

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

## Point Transformers

#### [[2011.00931\] Point Transformer (arxiv.org)](https://arxiv.org/abs/2011.00931)

* 分为两支分别去capture local和global的feature，其中local feature，设计了一个SortNet(select points on a learnt score)去pool出top-k的点； attention是用来融合glocal和local feature的
*  强调了permutation invariant，貌似是motivation的来源？attn过程本身是permutation-invariant的，但是output仍然不是permutation-invariant的？所以提出了一个sortnet去给出ordered subset(containing latent local feature)， attention output is permutation-invariant & ordered？
* RW中提到了一个Set Transformer which adapt transformer to process unordered set elements
* 介绍了一种对于两个Set的Cross-multi-head attention方法 A^MH(P,Q)
  * （表示输出是ordered且depend on the order of the P ）
  * 查阅了代码是Q用LocalFeature生成，然后KV用GlobalFeature生成？
* Method:
  1. SortNet: 一个row-wise MLP给降维到每个点一个值，然后取top-K(with sort),然后对每个点，以欧式距离r做grouping；然后将score和local feature相concat得到最后的结果
  2. Glocal Feature Gen. : use MSG(multi-scale grouping): subsample with FPS, find neighbour to aggregate
  3. Local-Global Attention(目的是relate the local & global feature set):
     * Cross-Attn(SelfAttn(F_local), SelfAttn(F_global))



## LayerNorm

1. [Rethinking Batch Normalization](https://arxiv.org/pdf/2003.07845.pdf)
   * 由于在用BN的transformer训练的时候，batch的mean和var一直在振荡，偏离全局的running-static
   * 提出了一种改进，不用zero-mean，用quadratic-mean来代替方差的位置
   * (可能因为Text与Image的输入数据的batch组成本身会有一些区别)
2. [On Layer Normalization in the Transformer Architecture. ICML 2020](https://arxiv.org/abs/2002.04745)
   * transformer需要很好的warmup lr才能更好的训练
     * 认为是常用的Adam由于在训练的一开始样本的差距过大，调整学习率的方差也过大了，导致了出现问题
     * 后续这篇paper question了这个观点。
   * 分析了post-LN以及pre-LN的方法：
     * 原始的ver LN是放在residue的add之后的
     * post的最后一层的梯度很大，各层之间梯度不稳定
     * 原因：各子层之后的 Layer Normalization 层会使得各层的输入尺度与层数 L 无关，因此当 Layer Normalization 对梯度进行归一化时，也应当与层数 L 无关。
   * 将 Layer Normalization 放到残差连接中的两个子层之前，并在最后的输出之前也用一个LayerNorm
     * 造成了对每个层数的梯度范数近似不变



## Mateirals

- [一个非常育婴的基础教学](https://github.com/BSlience/transformer-all-in-one)

