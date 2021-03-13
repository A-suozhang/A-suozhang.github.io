# [DADA: Differentiable Automatic Data Augmentation](https://arxiv.org/abs/2003.03780v3)

* 🔑 Key:         

use darts-like for search the aug strategy

* 🎓 Source: 

PKU & Edingburg

* 🌱 Motivation: 

目前的搜索DataAug的方法都过于InEfficient，比如RandAug(用RL)或者是用Evo的(就对应着几种NAS的flow)

* 💊 Methodology: 

1. SS follow的是AA的

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210116124744.png)

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210116131929.png)

一个Policy P包含了N种SubPolicys s，它们服从了Categorical Disrtribution，互相Parallel，一个Subpolicy包含了N个Operation,每个operation包含了发生的概率P以及幅度MA，这些operation是Sequential的，服从了Bernoulli分布

2. Gumbel Sampling of Aug Policy(对于grad esitimator，改了Gumbel为Relax的)

与SNAS一样的Gumbel sampling方式，采用了StraightThrough，前向的时候用argmax的discrete，反向的时候用differentiable的variable
对categorical以及bernoulli都做了reparameterization

由于其中的一些aug方式的matitude并不对可导，比如说旋转，所以用了StraightThrough，对于这个
![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210116125654.png)

提出了一种RELAX的grad estimator，用的是一个可导的NN作为surrogate loss(?貌似是另外独立加入了一个MLP)

看起来它的train flow是直接用一个Bi-level Opt到最后，同时优化weight和aug(train集合训weight，valid集合训aug指导用valid loss)，然后直接拿过来用

* 📐 Exps:

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210116132736.png)

用WRN-40做搜索，后续training的时候加上了ShakeShake之类的其他提点数工具
训练中只对train set的数据用了aug,只搜索20个epoch，之后怎么办？(是保持这个sample，还是使用这种aug一直到最后？) - 看代码貌似是导出了一个genotype然后一直用这个
?-说是用valid-loss指导训练，但是valid的时候并没有用dataaug

* 💡 Ideas:  

1. 看起来它就是一半train训练aug一半valid训练weight，然后训练weight的时候没有用aug，只是训练而已,这样和正常DARTS的本质区别在于，正常DARTS在训练weight的时候也有sample,并且只训练sample过的weight。它这里不采样是否合理?



# [AutoAugment: Learning Augmentation Policies from Data](http://arxiv.org/abs/1805.09501)

- RL for Search

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210116140406.png)
![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210116140604.png)

# [Fast AutoAugment](http://arxiv.org/abs/1905.00397)

- faster search based on Density Matching

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210116141216.png)


# [PBA:Population Based Augmentation: Efficient Learning of Augmentation Policy Schedules](http://arxiv.org/abs/1905.05393)

- Evo for search


