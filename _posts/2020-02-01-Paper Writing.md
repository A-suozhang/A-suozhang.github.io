---
layout:     post                    # 使用的布局（不需要改）
title:      Paper Wrting 心得 & Digest           # 标题 
subtitle:   高考英语作文都被说口语化的人怎么写文章    #副标题
date:       2020-04-21            # 时间
author:     tianchen                      # 作者
header-img:  img/1_28/nantong-3.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 常用
     - 重新学习
---


# LaTex

> Linux下安装```sudo apt-get install texlive-full cjk-latex latex-cjk-chinese```

* 对一个section用```\label{sec:method-grouping}```来指定，在别的地方用```~\ref{sec:method-grouping}```
* 在cite前面加一个```~```表示小于一个空格的空间
* 排图片一般默认[ht]，h表示是当前位置，t表示在最上面（指的是下一页的最上面）
* 表格中cmidr，表示加一部分横线 \cmidrule(lr){1-2}
  * \hline是一整条线
* [合并表格](https://blog.csdn.net/sinat_36301420/article/details/79334767)
* ```\multirow{2}{*}{$n_i$}``` 当表格同一行的东西占的列数目不一样的时候
* [常见错误](https://www.weibo.com/ttarticle/p/show?id=2309403955741387052924)
* [Table相关](https://zhuanlan.zhihu.com/p/19749566)
* Fig. （大写，加点）
* 特有名词，比如GPU，以及方法AMC需要大写
* 两张图并列(注意这种方式两张图标号是a/b，但是如果用```\begin{subfigure}```的话标注就是新的fig)

``` 

\begin{figure*}[th]
  % include first image
  \subfigure[]{
    \includegraphics[width=0.45\linewidth]{eccv2020kit/figs/layer_nasbench101.pdf} 
    \label{fig:layers_nb101}
  }
  \subfigure[]{
    \includegraphics[width=0.45\linewidth]{eccv2020kit/figs/layer_nasbench201.pdf} 
    \label{fig:layers_nb201}
  }
\caption{The effect of the number of GCN or GATES layers. (a) Experiments on NAS-Bench-101. The proportion of training samples is 0.1\% (381 training architectures, 42362 testing architectures). (b) Experiments on NAS-Bench-201. The proportion of training samples is 10\% (781 training architectures, 7812 testing architectures).}
\label{fig:gates_layers}
\end{figure*}

```

* 当内容实在长需要压空间的时候，在fig或者table中加```\vspace{-10pt}```来压缩空间

# Digest

### 常用词语及Synonym整理

* ample 足够的，多的
* excel 超过
* extensive 广泛的
* subpar 次佳
* paradigm 范例
* scheme 方式
* generic 一般
* derive 获得
* compelling 引人瞩目的
* consolidate 巩固
* coincide 一致
* deviation 误差
* counteractive 抵消
* need-demand-requirement-desire 需求
* derivation 推导

### 常用短语

* In light of above
* is based on 可以用
* seamlessly incorporated into / work in parallel with
  * 前者的主语稍微小一些
* seek to do
* be prone to 有倾向
* put forward
* deem to 认为
* sth. degree (Lr decay degree)
* engage/involve with 涉及


### 常用语句

* To sum up, we make the following contributions:
* The rest of this paper is organized as follows. The related studies are introduced in Sec.~\ref{sec:related}. In Sec.~\ref{sec:methods}, we introduced our fixed-point training. Then in Sec.~\ref{sec:exp}, the effectiveness of our method is illustrated by experiments. We further discuss XXX in Sec.~\ref{sec:discuss}.Finally, we conclude our work in Sec.~\ref{sec:conclusion}.
* ad-hoc - (for this purpose only)

# 心得 (他人经验)

* 逻辑链完全，让🐵都能看懂
* 多抄别人句子
* 先啰嗦，不要省主语
* 每一段的第一句话要总（最好是总分总）
* method中需要给出challenge
* 不要写进去节外生枝的
* 对应，图里有的一定要对应到文字
* 每个段还有一个单词，缩句缩排版
* 用连字符来把每一行填满
* 需要检查的
  * 名词不是复数就要加a或者the
  * 大小写！
* 图要放在最上面
* 截稿前注意备份overleaf（活用overleaf的history功能）
* 在最后统一check一些表达
* "x"用```$\times$``` dollar号不能忘不然会格式崩坏
* 把重要的东西往前放，然后再说为什么
* 有些文章会用一个单独的section来解释名词(可能当领域比较混搭的时候)
  * 以及一些文章在exp部分会优先将各个baseline介绍一哈

---

### 2020-03-06 ECCV 心得

* 现在是2020-03-06-03:25 文章交了，总结一下几点心得吧
  * 现在自己写文章知道这句话应该写什么但是还是表达不好…
  * grammarly check
    * 一般最后grammarly查出来的： 1. 三单 2. the/a 3. 极少的搭配
    * the/a 加了没有check
    * 在提交之前统一一下一些东西的用法
  * margin是个小bitch，我还是不会调
  * 相对minor的点如果会引起误解建议省去，还不大会出现文章写不满，只有压不下来
  * 早点找**没有参与过工作的，没有领域背景的**同志看文章，会有新的发现(啊你method看不懂啊)
  * 不要吝啬强调，文章的重点就paraphrase反复的说
  * 改图的一些心得
    * 对称是好文明，不仅是位置对称，还有字体字号的对应
    * 配色，这个倒是可以展开讲，目前我的体会是，饱和度拉低，色系倒是可以稍微跨越一些
    * 字体大小放大！
    * 实际放到文中的图里的字大小，和ppt里字大小不完全相关联，有时候需要把图压扁或者拉长才是真正关键的，因为论文格式里页宽就这么多

### 2020-04-20 Rebuttal

* 对于给高分的，先舔，表示XX很重要，然后说我们考虑了XX，没做什么是为什么，还会做什么

### 2020-05-26

* 阅读了[浅谈Rebuttal](https://zhuanlan.zhihu.com/p/104298923)
* 体会审稿人的意图，从他的角度出发
  * 审稿人喷只是incremental/参考，要澄清某部分的价值
  * 如果某些贱人只拿Novelty有限说事，说明它找不到硬伤
    * 还是只能说明创新点在哪里,集中说明我们的创新点
  * 喷「factual error」：审稿人一旦找出文章的事实性错误，作者不妨大方承认，并表示感谢，同时表示会在final version中更正错误；另一种情况是，可能就是因为作者自己没写明白，才使得审稿人错误理解，如此，也可大方承认，说“我们已经修改了这部分描述，实际上是这样做的，并不是你理解的那样，blabla
  * Rebuttal时若发现审稿人的factual error，如ta提出的某个观点有显然错误、提出需要对比的数据集显然不是该领域常用的数据等，作者可在rebuttal回应此人时首先指出其错误
  * rebuttal时不要漏点，要逐点回应做到有问必答。若因篇幅有限，可将类似的意见合成一点，万不可因篇幅有限擅自删除一些要点或遗漏要点，以免造成含糊不清、浑水摸鱼之嫌
* **要有问必答**




# Resources

## Docs

* 石墨
* 腾讯

## Writing

* Grammarly
* [Liggle](https://linggle.com/)
* [Synonym](https://www.synonym.com/synonyms/)
* [New Better Synonym](https://www.thesaurus.com/browse/optimize?s=t)
* 有道翻译


## Bib
* [ResearchGate](https://www.researchgate.net/?_sg=9D8JkWoEvbx5lKepanzgM0bx2GcheNWitXm5LIwKovHl43ewI72-pQS0vPCyRAmRJA37PppxLEoD)
* [Semantic Scholar](https://www.semanticscholar.org/)


## Plot

* [Color Hex](https://www.color-hex.com/)
* [Matplotlib Doc](https://matplotlib.org/contents.html)
  * [Cmap](https://matplotlib.org/3.1.1/tutorials/colors/colormaps.html)
  * [Marker](https://matplotlib.org/3.1.1/api/markers_api.html#module-matplotlib.markers)
* [Matplotlib Tutorial(Not Official)](https://riptutorial.com/matplotlib/)
* [Mathematcia Doc](https://reference.wolfram.com/language/)
