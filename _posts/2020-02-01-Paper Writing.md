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

### 使用姿势


* 一般flow是xelatex-bibtex-xelatex
	* 用xelatex来创建(pdflatex几乎同等)
	* 用bibtex xx.aux来建立ref
* 文件意义
	* .tex是主要的代码文件
	* .bib内部包含了从google scholar等地方获取的引用
	* .aux首次编译的时候产生
		* .blg也是生成的中间文件
	* .bbl bibtex所输出的bib
	* 一些模板会包含比如说
		* ruler.sty
		* splncs04.bst
		* llncs.cls

* 有的时候会因为pdf文件被占用而导致失败，导致了bib炸了


### Table 

* basic template


> [overleaf-guide](https://no.overleaf.com/learn/latex/Tables)

```
\begin{table*}[hb]   % hb means here-bottom
\centering
\caption{head of the table}
\label{tab: some_table}    % for reference \cite{tab:xx}
\begin{tabular}{c|c|c|c}   % denote the vlines
	something & something & something & something \\
	...
\end{tabular}
\end{table}
```

* use \hline \vline \cline{1-2} if lines are needed
* 默认情况的vline只有在对应位置的地方有东西的时候才会出现
	* 所以有的时候要写空的 ```& & & & \\```
* for multi-row - use ```\userpackage{multirow}```

```
\multirow{2}{*}{some word} A1 & B1 & C1 \\
						   A2 & B2 & C2 \\
\hline
```


### Formula

* 用\begin{equation} \end{equation} - 讲道理它会自己标上序号
* 在公式前后加上\begin{split} \end{split} 用作分割
* 对于两条公式的对齐，手动在内部加上 & - 公式里的&的位置会自动对齐
* 对于s.t.这样的东西需要给它加上\mbox{}， 并且保持一个空格

---

> 打公式常用的一些符号的cheat sheet,[更完整](https://www.rpi.edu/dept/arc/training/latex/LaTeX_symbols.pdf)

* `\int` - 积分
* `\l(g)eq` - 大于等于号
* `\cdot` - 点乘
* `\times` - 叉乘
* `\infty` - 无限符号

### checkmark

```
usepackage{tikz}
\def\checkmark{\tikz\fill[scale=0.4](0,.35) -- (.25,0) -- (1,.7) -- (.25,.15) -- cycle;} 
```

### Tips

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
* 调整图片格式实在是太痛苦了！很容易一张图就会被卡到最后去，整个格式都会崩溃
	* 目前发现可以用```[H]```大写的H来强制将其锁在Here！
* \footnote{} 脚注
* 表示星号要用```$^*$```
* 引用链接  ```\href{http://www.sharelatex.com}{Something Linky}```
* alias的方式 ```\newcommand{\method}{BARS\xspace}```
	* xspace是表示在需要的时候加空格

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
* 在标题中换行 \title{something \protect\\ something}
* 有的时候编译莫名错误 ```File ended while scanning use of ```
	* 首先删除本次编译产生的aux，brf，bbl文件
	* 然后记得要关闭pdf解决文件占用问题

# Digest

### 常用词语及Synonym整理

* ample 足够的，多的
* alleviate 缓和
* asymptotic 渐进的
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
* facilicate 促进，帮助
* neutralize 抵消
* implication 含义
* anticipate 预计
* contemporary 当代
* prohibitive 禁止的(很困难的)

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
* engage/involve with 涉及 （engage with -> involve）
* in-depth comparison
* ouptut response - 输出的激活值

### 常用缩写

* ```et al.``` - 拉丁语中的et alia，表示作者时候的省略
	* et后面不加.因为et本身不是缩写
	* 如果是在句子最后，不需要两个.
	* 如果后面的不是句号，那后面的.还是需要的 
* ```etc.``` - 表示等等
	* et cetera - 一般放在举例的最后，表示前面的例子还没有举完
	* 与上面的区别是人用et al，没有生命的用etc
	* 前面需要有逗号！和上面那个不一样
* ```e.g.``` - for example
	* exampli gratia
	* 代替for instance, such as
	* 前面有逗号，最后有句号以及逗号   
* ```i.e.``` - 也就是
	* id est
	* 等同that is, in other words
	* 也是最后有点和逗号

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


### 2020-05-01 挂arxiv

* 注意arxiv需要的是latex source code
* 在文件的一开始需要指定```\pdfoutput=1```
	* 即使是在tex编译的时候显示error也是按照这个规则来做
* 打包成一个压缩包包含所有source code，需要不含subfolder，里面直接是内容
	* 主tex文件是要是```ms.tex```
* 注意arxiv并不会给你跑bibtex
	* 所以提交的时候一定要将bbl文件给包含了，否则会没有引用信息！

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

### 2020-06-20

* Review Trans Paper
* 有什么不懂的就加Question
  * 给Major Revision / Minor Revision 
* Template

```
Decision: Major revision

Comments:
The authors argue that imposing uniform regularizations on filters during structured pruning is problematic. And they propose to modify the existing regularization-based structured pruning methods, by weighting the regularization of different filters based on the saliency scores. The saliency score is calculated as ||grad x scale||^2 / FLOPs reduction.

Strength:
The accuracy results on CIFAR-10 and ImageNet are impressive. 

Weakness:
You should analysis the computational efficiency of the overall pruning process. In you abstract, you state that the overhead of calculating the saliency is minimum, however, there is an inner iteration to calculate the saliency scores every epoch during SASL, which might be time-consuming. Also, the post-processing pruning and fine-tuning may be time-consuming. I'm aware that you utilize a hard-mining technique to accelerate the pruning process, and in Ablation 4), you said “hard sample mining strategy can not only reduce the complexity overhead for the saliency estimation“. I would like to see a quantitive study on the computational efficiency of your proposed framework.

The description of the method and experiment settings is not very clear, here are some queries about the method and experimental settings:
- How do you “classify” the filters into different “ hierarchy”? Define thresholds, or just uniform distribute them according to the ranking?
- How many iterations are there in the iterative pruning and fine-tuning process? In each iteration, how many filters are discarded?
- In the ablation study 2), how do you change the base regularization value according to the number of classes? Did you experiment with class number larger than 5?
- In SASL* (a.k.a, the aggressive scheme), what hyper-parameters are tuned? The base regularization value in SASL, or only the post-processing iterative pruning process?
- How do you handle skip connections?


Other comments:
- Page 5 line 24, Page 8 line 30 (right column): wrong quotation marks
- I don’t think hierarchy is a very proper terminology for describing different saliency levels.


From my perspective, i suppose using the product of the gradient magnitude and the weight/activation magnitude as the saliency signal is common in early studies, e.g., [1][2]. Thus, the main contribution of this paper is to propose to use the quotient of the commonly used saliency score and the FLOPs reduction as the new saliency score, and then, the saliency scores are used to determine the weighting value for the regularizer.

[1] P. Molchanov, S. Tyree, T. Karras, T. Aila, and J. Kautz, Pruning convolutional neural networks for resource efficient inference, ICLR 2017.
[2] Michael Figurnov , Aijan Ibraimova, Dmitry Vetrov, and Pushmeet Kohli, PerforatedCNNs: Acceleration through Elimination of Redundant Convolutions, NIPS 2016.
```


```
This paper proposes several techniques to improve dense video caption performance, including an event-level feature fusion, a scene-level topic predictor, an NMS procedure that takes both temporal and linguistic similarity into consideration. By combining these techniques, this paper achieves very good results. Also, this paper conducts comprehensive ablation studies on the proposed techniques.

Basically, I think this is a good paper. Although the event-level feature fusion method is simple, the ablation studies have demonstrated its effectiveness. And, I like the idea of using topic model to discover latent topics, and then using the topic distribution of each video as the scene-level training signal. And it’s good to see that the ablation study demonstrates its effectiveness on the evaluation metrics. Is this idea original? 

I have some queries:
- In equation (2), why do you use asymmetric linear embedding layers, W_Q, and W_K, this doesn’t seem like a reasonable distance metric.
- Page3 line39 column right, you say that “the low-dim vector P may not be easily separable”. I think separable is not a proper word here, since we are not solving a classification problem.
```

```
TCSVT review: multi-feature adaptive late fusion image retrieval based on cross-entropy normalization


Review confidence level: 4
Technical content:
novelty: 1
Tutorial: 1
Technical correctness: 1
Technical depth: 1
Application: 1

Presentation:
clarity: 1
organization: 1
conciseness: 1
english: 1
references: 2

Decision: Reject

Comments:
This paper proposes to select reference curves for each query image by minimizing the “cross-entropy”, and then use the calibrated similarity curves to fuse multiple features.

Basically, I think there is a lot to be improved on this paper. 

(1) Writing and Clarity
This paper is poorly written. There are many grammar mistakes, many sentences cannot convey the points, and many terminologies are not used correctly. The authors should check the grammar, and polish the presentation.

(2) Organization and conciseness
      Related work: The summary of the related work is not well organized, the logic chain between subsections is weak.
      Method: The organization of the method section is not good, it’s hard to follow your method description and capture the crucial points, and I must read for the second time to capture what you mean. For an example, the title of III.C “Effect Analysis of Cross-Entropy Normalization” does not fit with the content.

(3) Technical correctness and depth
     I have some doubts about the method. The description in the paper seems to indicates that the S curve for each feature is normalized, and this paper aims to find a reference curve that can calibrate each feature’s curve by fitting its tail. However, It seems from Eq.4 that S_i(u:v) is normalized across different features, maybe the notation is wrong that the summation is not across the index “i”?  If so, the summation of S_i(u, v) across the u-v segment should be less than 1 instead of equal 1.
     
     This method is purely heuristic and empirical, I don’t think the derivation about the positiveness of the “relative entropy” is suitable in a paper, since it’s a fact that is well known in textbooks. Thus, I would recommend that you remove the two theorems in your paper.

      Why do you use “area under curve” to calculate the fusion weights. The only place that mentions this intuition is the II.D section where you discuss that [17] utilized this intuition. Can you provide more theoretical or intuitive background , or at least, can you experiment with other alternatives to demonstrate the rationality of this weighting scheme. 

     The word “complementarity” is repeatedly used in the paper, you emphasize that your method of selecting the reference curve can “optimize” the complementarity. However,  I fail to capture the logic behind this statement. According to your description in III.D, it seems like you think minimizing the “complementarity” of the reference curve and the current feature’s similarity curve is a way to “optimize” the complementarity? I suppose that utilizing feature complementarity means that you should choose features that maximally complement each other, and this is not related to the proposed method.

(4) Experiments, Application of the method
     This method might not be so useful in application. The proposed method is an incremental one, and brings extra memory footprints and query latency overhead. 

     In IV.G, you said that “we’re not going to make a direct comparison on the query time”. I don’t think the differences in the computer’s performance or the feature number is the reason for not doing the quantitive latency comparison, since all these variables can be controlled. So, you should compare the query time with more baselines.
```


### ECCV Camera Ready

> 开始准备eccv2020的camera ready了

* 需要提交的内容有
	* source code(latex) + camera ready的pdf
	* copyright pdf - 需要签名
	* supplmentary material


* gates
	* baseline文字的图片和文字改一下


### CVPR2021 -  2020-11-28

* 第二次完整的写文章，流水账一下
* 首先是在CMT上注册获得paper-id,占坑
	- 自己写的时候语法错误好多，还需要锻炼，包括文章的表达方式，以后读文章的时候还需要多积累
	- 开着grammarly写把
* 关于看ddl可以加个群或者用[AI Deadlines](https://aideadlin.es/) - 可以不用搞复杂的时区换算了


# Resources

## Docs

* 石墨
* 腾讯
* Google Docs
* Notion

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
* [这篇文章图不错](https://arxiv.org/pdf/2006.05467.pdf)
