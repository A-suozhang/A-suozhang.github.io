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

# Paper Write 注意点

# 最终检查的细节

1. 一些Term的一致性
2. Fig. Figure Tab. Table 是否一致
3. 公式/图表的caption最后有没有标点
4. e.g. / i.e. 后面可以加逗号可以加
5. 单双引号 
6. 括号前面的空格  
  - ~~(一个10块红包)~~

# 相对Meta的一些知识

- 文章8页没有写满，或者是中间有太多的换段只有几个单词的情况，会被人认为 **不够丰满**
- 整个文章应该是一个Hierachical的结构，Abs里最重故事说理，intro里部分展开，method里尽量展开

- 画图：
  - 不要想着能够包含所有信息，突出最关键的，要Clean
  - Teaser(开头的图) 一定要简单，就只说最核心的一个点

- 实验结果：
  - 要全面，考虑别人会如何Question
  - 有些看起来一般的要不就干脆别放
  - 仔细考虑**如何排布你的实验数据**让它看起来好

- 文字层面
  - 每个概念(容易混淆的，自定义的) 第一次出现的时候加一句话解释一下 （对于觉得很重要的可以用脚注 - 一般不用）
    - `\footnote{We summarize the geometric pattern and density of voxels as their ``geometric information''.}`
  - 公式书写之前，每一个字母都一定要出现过，不要让别人Lost
  - 避免一些领域内有争议/不显然的结论，出现的话一定要有引用
  - 不要跳逻辑！写结论或者insight分析，只写当前层的，不要直接导到核心观点


# 文字相关积累

## 写作注意点(Explicit)

- 某些词可能会有特殊含义，可以上Google直接搜这个词，比如`resort to`负面
- 注意复数性质会延申到后面 
- and
  - 前后的**时态一致**
  - 表示同级，前后两者应该是一个Level的  
    - ```$\cap$'' calculates the intersection of these two regions and is normalized by the geometric region size $N(\varphi(\theta_{ij}))$` 这句里后半句的主语是这个cap操作，而不是前面紧跟的东西
  - 注意用and连接的句子的**主语**到底是什么
- the
  - 名词没有复数，或者没有形容词描述，就要加the
  - 特质某个东西，可以用the(与用a的辨析)

### 常用词语及Synonym整理

* ample 足够的，多的
* alleviate 缓和
* asymptotic 渐进的
* excel 超过
* extensive 广泛的
* subpar 次佳
* paradigm 范例
* scheme 方式
* generic 一般(普遍)
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
* exploit 利用起来
* leverage 利用xxx
* specifically(More concretely) 详细展开
* incorporate 嵌入,引入
* granularity 粒度(用在量化的粒度的时候)

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
* ad-hoc - (for this purpose only)
* serve-as:  作为
* a surge of research efforts

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

# 公式书写

- TODO：感觉自己对这个没有系统认知，不知道应该用什么方式来表达


# Tools

1. detex以搭配grammarly使用： 因为grammarly不能很好的处理latex，所以学习了detex工具来将latex文件输出成raw text，将其拷贝如grammarly就不会报错了(美中不足是这样不能改完之后粘贴回原文了)

- Detex  `detex PaperForReview.tex | grep -v '^\s*$' > PaperForReview.raw`
  - sudo apt-get install libfl-dev
  - sudo apt-get install make gcc flex
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20211118112458.png)
  - [](https://www.cnblogs.com/lovychen/p/7429682.html)
  - [DeTex 方法](http://keyvaneslami.com/blog/knowledge/opendetex-package-remove-tex-constructs)

# 相关资源以及网站

## Docs

* 石墨
* 腾讯
* Google Docs
* Notion

## Writing

* Grammarly
* [Liggle](https://linggle.com/)： 查句子搭配的网站
* [Synonym](https://www.synonym.com/synonyms/) 查同义词的网站，但是感觉用处不是很大
* [New Better Synonym](https://www.thesaurus.com/browse/optimize?s=t)
* [ESODA](http://www.esoda.org/)： 贵系做的网站，能包含一些找句子等的功能
* 有道翻译
* 直接google这个单词，看释义以及隐藏含义


## Bib
* [ResearchGate](https://www.researchgate.net/?_sg=9D8JkWoEvbx5lKepanzgM0bx2GcheNWitXm5LIwKovHl43ewI72-pQS0vPCyRAmRJA37PppxLEoD)
* [Semantic Scholar](https://www.semanticscholar.org/)
* [Wiki-Cite](https://irl.github.io/bibwiki/)


## Plot

* [Color Hex](https://www.color-hex.com/)
* [Matplotlib Doc](https://matplotlib.org/contents.html)
  * [Cmap](https://matplotlib.org/3.1.1/tutorials/colors/colormaps.html)
  * [Marker](https://matplotlib.org/3.1.1/api/markers_api.html#module-matplotlib.markers)
* [Matplotlib Tutorial(Not Official)](https://riptutorial.com/matplotlib/)
* [Mathematcia Doc](https://reference.wolfram.com/language/)
* [这篇文章图不错](https://arxiv.org/pdf/2006.05467.pdf)


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

# Rebuttal

> 参考 [如何写学术论文的rebuttal？ - 王晋东不在家的回答 - 知乎](https://www.zhihu.com/question/32055996/answer/187612713)，[Rebuttal指南](https://mp.weixin.qq.com/s/ZCHm7qGebxWiI54eHvEY3Q)

### 从思路上

首先要定义清楚，reviewer的类型，reviewer的给分可能并不一定表示他的potential的态度(这也就是为什么那么多文章能够334逆袭这样子)。Reviewer实际想要拒绝你的理由，或者实际的评估，其实并不一定是给出的comment。有可能是因为文章和他的taste不符，但是又没有找到硬伤，只能general的评价。

1. Type1：（大概率是WR）有可能是因为文章和他的taste不符，但是又没有找到硬伤，只能general的评价： 可能给出一些很general，并不是很本质的漏洞，态度偏打压；**基本就是寄了**
2. Type2：（大概率是WR）很了解你的领域，能够找到甚至挑剔的提出一些很细节的点： 大概率是小同行，想搞你，**基本寄了**
3. Type3：（B或者是WR）对你的文章有一些误解/或者是提出了一些有意义，但是不是硬伤的问题。可能会给一些相对模糊的话，比如”some issues in the paper should be fixed before being fully accepted”，或者是"may raise score"。**一定要抓住，划重点解释清楚他的误解/问题**

要抓住重点，团结可能团结的人，可以利用其他Reviewer的观点来against others。
一定要show enough respect，舔舒服了
一定不能出现主观而且不公认的语句

### 从实践上


- Rebuttal的目的不需要有很强的逻辑性和优美的writing，而是要很直接明确的把问题点出来！
  - 每个回答的第一句**直接**回答甚至更正R的理解和问题，一定要highlight我们想表达的重点，免得被认为是在答非所问或者绕问题
  - 不能漏问题，每个都要回答！
  - R的理解有错误可以用`there might be sime misunderstanding`
  - 对于有需要对比其他文章的(novelty)层面，不要从motivation层面说我们处理的是不同问题，而是要说他们的方法为何不能apply到我们的场景下

- 每个R会有7-8篇文章，甚至有emergency能有10以上，所以大概率看rebuttal很快，甚至不会回头去ref自己系统里的问题，而是只看rebuttal的pdf(有可能都忘记自己问的啥问题了)
  - 在pdf中写清楚reviewer的问题，并且把重要的提前
  - 不用重复回答，可以提取共性问题，merge小问题到一个点，Please Ref R2Q3
  - 可以简写和ref文章/Appendix的其他部分

- 提出的一些要求可以先答应着能做，然后final version加不加就是你的主动权了
  - 当然如果R要求的实验结果或者讨论不说，得有明确理由为啥没有，不然就可能被认为是逃避问题

- 对于一些实际上自己错误或者不足的点，大方承认；表示自己会修正；(如果他认为问题比较大，就*解释为什么这个问题不本质，或者可以怎么被fix*)

- 如果真的命中了比较关键的缺点，一定**不能逃避**！可以承认一下，表示我们会对这个点作为future work来改进。
  - 当然如果提出问题的人给Accept，直接表示赞同他的观点，并且表示We consider your concern and will add it into the limitation of our work. (可能大概率是小同行，希望这个领域多点人)

- 如果去喷实验，最好的方法是补充他要的实验，如果补不上，说明resource constraint，并且表示会supplement in future version. 如果还是不行的话，强调自己的两点和contribution，我们focus on xxx，相比于一些点数，xxx更matter

## 常用素材

* 开头的客套话

```We thank all the reviewers 1 for acknowledging our novel contributions and providing valuable feedback.```
```we address the common concerns followed by detailed comments from each reviewer```
```We thank all the reviewers for their insightful and constructive feedbacks! We will revise the final version according to this rebuttal.```
```We’ll carefully proofread the paper and fix the typos in the final version.```
```Thanks for the suggestion.We’ll make the claim more rigorous in our final version```
	
* 委婉的喷

```We suspect / There may be some misunderstanding```

* 给AC说

```
We appreciate the constructive comments given by the reviewers.However, we’d like to mention thataccording to the comments, Reviewer 4’s knowledge about GCNdoes not seem to align with other reviewers, e.g., asking whether better minibatch gradients of GCN is a problem, and asking why GCNs need dropout.Moreover, there seems to be misunderstanding on Theorem 1.
```

### Examples:
1. [./others/rebuttal/bars.md](./others/rebuttal/bars.md)
2. [./others/rebuttal/mojito.pdf](./others/rebuttal/mojito.pdf)
3. [./others/rebuttal/codedvtr.md](./others/rebuttal/codedvtr.md)

---
# 流水账


> 下面是年轻时候写paper的一些心得体会，尴尬的很，放到最下面来了

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


### 2020-04-20 ICML2020 Rebuttal

* 对于给高分的，先舔，表示XX很重要，然后说我们考虑了XX，没做什么是为什么，还会做什么

### 2020-05-26 ECCV Rebuttal 

* 阅读了[浅谈Rebuttal](https://zhuanlan.zhihu.com/p/104298923)
* 体会审稿人的意图，从他的角度出发
  * 审稿人喷只是incremental/参考，要澄清某部分的价值
  * 如果某些贱人只拿Novelty有限说事，说明它找不到硬伤
    * 还是只能说明创新点在哪里,集中说明我们的创新点
  * 喷「factual error」：审稿人一旦找出文章的事实性错误，作者不妨大方承认，并表示感谢，同时表示会在final version中更正错误；另一种情况是，可能就是因为作者自己没写明白，才使得审稿人错误理解，如此，也可大方承认，说“我们已经修改了这部分描述，实际上是这样做的，并不是你理解的那样，blabla
  * Rebuttal时若发现审稿人的factual error，如ta提出的某个观点有显然错误、提出需要对比的数据集显然不是该领域常用的数据等，作者可在rebuttal回应此人时首先指出其错误
  * rebuttal时不要漏点，要逐点回应做到有问必答。若因篇幅有限，可将类似的意见合成一点，万不可因篇幅有限擅自删除一些要点或遗漏要点，以免造成含糊不清、浑水摸鱼之嫌
* **要有问必答**

### 2020-06-20 Trans Review 

* Review Trans Paper
* 有什么不懂的就加Question
  * 给Major Revision / Minor Revision 
* [Example](./others/rebuttal/trans-review.md)

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

### 2021-08-05 TIP Recycle

1. 对每个reviewer需要给一个Detailed Contribution

>  [你了解作者中的贡献者角色分类法（CRediT）吗？ - WORDVICE的文章 - 知乎](https://zhuanlan.zhihu.com/p/374106127)

```
* Tianchen Zhao: Conceptualization and BNN related papers investigation, software design(BNN & NAS part), All Experimental Designs, Writing – original draft
* Xuefei Ning: Conceptualization and NAS related papers investigation, software design(NAS part),  All Experimental Designs, Writing – original draft
* Xiangsheng Shi: Software design and Experiments about BNN baseline methods, Experimental results Visualization
* Songyi Yang: Software design and Experiments about NAS baseline methods, Writing – original draft
* Shuang Liang: Validation of the searched BNN, Writing - Review & Editing
* Peng Lei: Validation of the effectiveness of the NAS method, Writing - Review & Editing
* Jianfei Chen: Methodology verification, Writing - Review & Editing
* Huazhong Yang: Project administration
* Yu Wang: Resources, Project administration
```
### 2021-11-17 CVPR22 Submission以及准备Rebuttal

- 在投稿期间的
  - CV的会审稿人相对比较Lazy，图很重要！图需要画的美观以及包含想表达的信息(一个teaser，一个flow图，一个方法的细化图)，很多R可能就看一眼图就有了一个对方法自己的理解，而且很难改变！
  - 一开始teaser的图(这个是电影预告片的意思)，一定要简单，概括出最关键的点，让大家一眼能明白核心contribution（参考SwinTR）
  - 文中一定不要有可能会misleading的表述，R很可能先入为主而且很难修正意见
  - intro和RW额外小心不要overclaim或者说一些比较bold的表述，所有的主观评价性的表述都要有依据！否则会给人不好的映像
  - 与well-known的类似的文章一定要有对比，否则实验很容易被人question
  - 可以说Code will be released来增强印象
  - 方法的细节可以放appendix(但是按照这次的规律来看基本是没什么人去看…review里狂问)

- 等Review时候补充实验的时候，CV会的reviewer最喜欢问问题的方式
  1. 从Novelty角度，本身是仁者见仁的事情：
    - 继续重复我们的想法，尝试说明其重要性
    - 援引其他的文章也探索过这个问题，说明这个问题本身的
  2. 实验不够充分，不能说明你的观点
    - 补实验吧…一种是和你直接类似的paper的文章结果，另外一种是你的一个方法上的claim的ablation是否全
    - 关于加实验，CVPR的态度是R不能要求你加实验，并且因为这个拒你，但是可以自己补充实验结果来说明问题


