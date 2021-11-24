# Paper Reading MAE: Masked Autoencoders Are Scalable Vision Learners

>🎓 Source: He Kaiming FAIR

### 🔑 Key: 

- 设计了一种基于mask patch的pretext task，让AutoEncoder成为很好的SelfSup Learner
- Claim能learn high capacity model and generalize well.

### 🌱 Motivation: 
- motivation是从NLP的SelfSup的大模型开始讲起，说Vision为什么不能有

- 梳理了Image和Language的区别
	- Information Density: image large redundancy
	- Decoder in autoencoeder: image - predict pixel(rather low-level); language - predict missing work(high-level with semantic info), find that the NLP decoder is trivial(MLP), but image decoder plays a critical role

- Final Discussion中讲了一个故事就是language和image是nature不同的两种模态，所以需要被stress differently
	- MAE: remove patches and reconstruct pixels
	- 强调MAE learn到了rich context info

### 💊 Methodology:

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20211120215449.png)

- 用一组Autoencoder,设计了一个新的Pretext Task，对输入图片随机Patch大部分，用decoder去reconstruct the full image
- Masked(Asymmetric) AutoEncoder
	- Encoder applied on masked image 
		- Transformer layers, only applied on non-masked tokens(a fracture of the image)
	- Decoder trys to reconstruct the original clean picture from the latent representation
		- Also a series of TR layers, the input are the "full sized" picture:" (encoder output + mask embedding)
		- mask embeddings are all the same and learnt(like cls token, but replicated)
			- PosEnc added, otherwise no information for mask embedding
		- Only applied on the pretraining for the image-restoration task
		- lightweight, smaller decoder compared with the encoder
- Masking Scheme
	- ViT patching scheme
	- random sampled based-on uniform distribution without replacement(preventing the center bias: more masks near center)
- Reconstruction Loss
	- The decoder outputs N-patches feature, linear projection to N-pixel-per-token, then reshaped
	- MSE Loss
	- calculated only on masked patches like BERT
	- use patch normalized improve perf. (small trick)

### 📐 Exps: 

- **仅用ImageNet-1K(1N1K)** train ViT-Large取得最好的性能，且具有好的Transfer能力
- Surprisingly High optimal mask ratio: 75%
	- contrastive to BERT(15%)
	- shows different trends with LinearProb / Finetune for SelfSup Learnt Feature, however the optimal is around 75%
- Important Design: skip the masked patches for encoder, and use them for decoder
	- If the encoder used too, huge degredation(Pure image restoration)
- Cropping only aug

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20211120211146.png)

- "Linear Probing": use Linear SVM as unsupervised classifier(to test the acquire feature without label)
- Authors stresses that the "linear Probing" does not work well(despite popular), low correlation with finetuning 
	- in this papers exp, the actual perf work well on (40 ~ 80), however the linear grdually increases till sweet point
	- misses the pursuing of strong but nonlinear features
	- however, whole finetuning resource-consuming, so proposed 'partial finetuning'(only finetune the last K layers), when use 0 layers, it is reduced to linear probing

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20211120211538.png) 
![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20211120211508.png)

### 💡  Ideas: 

- 确实simple but effective
- 理解到了Language和Image做reconstruction的本质区别在哪里，get到了MAE这么做的一些Insight，但是不够通透还
	- 可以理解mask patch就是等价于NLP任务中去mask word(都是包含了semantic的信息)
	- 作者在强调由于他predict是dense的pixel(low-level),所以它learn到了很rich的context info(?)

---

> 参考了一些知乎回答之后

1. MAE可以认为是设计了一个pretext task,或者是一种objective，用来解决 TR本身牛逼但是难训练的问题
2. we are interesting to see 将各种加conv的变体加到MAE里怎么样?
3. why work?
	- 没有特别多的论证，只有一些insight，后续肯定会有人探索
4. 关于linear probing以及finetune的低correlation will affect SelfSup Learning community
	- 以及这种mask的scheme可能会overtake Contrastive Learning（that heavily relies on DataAug）
5. 反直觉： 
	- pixel-wise MSE reconstruction能work(做为好的pretext task)不合理，本身难学好
6. high mask ratio引起了质变？
	- 去噪，上色(impainting),补全等任务(generative)早就在用来做SelfSup了, 加mask这种也是NLP的标配
	- 在文中作者讲了一个high mask ratio和information density相关的故事(感觉比较general且间接，玄乎了点)

---

# Refs

- [original Paper](Masked Autoencoders Are Scalable Vision Learners)
- [知乎问题 - MAE](https://www.zhihu.com/question/498364155?rf=498364604)
- [如何评价微软提出的无监督视觉模型BEiT：ImageNet达到88.6，ADE20K达到57.0？ - 小小将的回答 - 知乎](https://www.zhihu.com/question/478187326/answer/2047466481)
- [Tutorials of SVM](https://www.freecodecamp.org/news/svm-machine-learning-tutorial-what-is-the-support-vector-machine-algorithm-explained-with-code-examples/)

---

# Related papers

> 这两篇都来自微软

1. [BEiT]： 早于MAE，但是没有只用IamgeNet的数据做的很好，也没有pursue很高的mask ratio
	- 参考Bert的方式对VisionPretraining做了探索
2. [SimMM]: 提出了一个框架式的东西，做了一系列分析，给出了一些knowledge，配合模型刷到了SOTA
	- achieves SOTA again, paired with SwinV2
	- Ablation了各种mask的patch大小
		- 采用中等大小的掩码块 32x32 same as patch size
	- 证明了head可以轻量化
		- 更大的head有更好的生成性能，但是不一定对任务性能或者是downstream task有用
	- 说明了预测任务可以用最简单的像素回归(Image Reconstruction) with L1 Loss
		- 对比了一些其他的方式比如  
			- Color Clustering(iGPT) 通过K-means将RGB分为512个cluster并作预测
			- Vision Tokenization(BEiT): 额外训练了一个discreteVAE，将patch转化为了token，以token作为分类目标
			- Hcannel-wsie color discretization: 对RGB通道分类，并离散化成一些bins，做分类





# Personal Guideline

1. 读完摘要填充Methodology的总览，以及Key, (Possible: 最好的实验结果)
2. 读intro填充motivation
3. （如果有需要(缺preliminary)读一下RW，看是否需要补充材料，否则跳）
4. 读methoddoly确认方法
5. 读EXP挑一些亮点
6. (以上步骤的话)中间可以插入，最后统一添加思考

> 这样流程精读一篇基本在30min左右(缺背景知识需要补的话可能更长)，如果只是草读的话，可以各个层面上简略


- (如果真的是特别牛逼的文章，可以额外学习一下他讲故事的方式)
