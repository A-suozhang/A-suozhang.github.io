---
layout:     post                    # 使用的布局（不需要改）
title:      Paper Wrting 心得 & Digest           # 标题 
subtitle:   高考英语作文都被说口语化的人怎么写文章    #副标题
date:       2020-02-02            # 时间
author:     tianchen                      # 作者
header-img:  img/1_28/nantong-3.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 常用
---


# LaTex

* 对一个section用```\label{sec:method-grouping}```来指定，在别的地方用```~\ref{sec:method-grouping}```
* 在cite前面加一个```~```表示小于一个空格的空间
* 多人协作

```
\usepackage{xcolor}
\usepackage{xspace}
\newcommand{\says}[3]{{\color{#3}\textbf{#1:} \emph{#2}\color{black}}\xspace}
\newcommand{\nxfsays}[1]{\says{nxf}{#1}{red}}
\newcommand{\ztcsays}[1]{\says{ztc}{#1}{blue}}
\newcommand{\zslsays}[1]{\says{zsl}{#1}{green}}
\newcommand{\zksays}[1]{\says{zk}{#1}{cyan}}

```


# Digest

### 常用词语及Synonym整理

* ample 足够的，多的
* excel 超过
* extensive 广泛的
* subpar 次佳

### 常用短语

* In light of above
* is based on 可以用
* seamlessly incorporated into / work in parallel with
  * 前者的主语稍微小一些
* seek to do
* be prone to 有倾向


### 常用语句

* To sum up, we make the following contributions:
* The rest of this paper is organized as follows. The related studies are introduced in Sec.~\ref{sec:related}. In Sec.~\ref{sec:methods}, we introduced our fixed-point training. Then in Sec.~\ref{sec:exp}, the effectiveness of our method is illustrated by experiments. We further discuss XXX  in Sec.~\ref{sec:discuss}.Finally, we conclude our work in Sec.~\ref{sec:conclusion}.

# 心得 (他人经验)

* 逻辑链完全，让🐵都能看懂
* 多抄别人句子
* 先啰嗦，不要省主语
* 每一段的第一句话要总（最好是总分总）
* method中需要给出challenge

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