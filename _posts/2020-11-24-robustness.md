---
layout:     post                    # 使用的布局（不需要改）
title:      Robustness相关           # 标题 
subtitle:   Strong！       #副标题
date:       2020-11-24            # 时间
author:     tianchen                      # 作者
header-img:  img/2020/cas.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - survey
---

## Survey - [A Survey of Privacy Attacks in Machine Learning](http://arxiv.org/abs/2007.07646)

* Privacy Preserving
	- NN model should be a black box with only input and output
	- attack could be at the model itself / acquire private training data from it 

* the threat model
	* 4 parts:
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201125094751.png)
	* different acess levels:just have the API / have full access to the model / training setting

* Attacks
	* black-box atatcks: usually atack pretrained model service from the cloud
	* white-box: have access to all model params / grads during training
	* partial whitebox: make strong assumptions to black box but no full access
	* ---
	* attacks training / inference: attacks training means active attacker

* Taxnomy of threats:
	1. Membership Inference: whether input x is used as part of training set
	2. Reconstrcut Attack: recreate samples from their label using the model
		* also called attribute inference / model inversion
	3. Property Inference Attacks: extract the dataset property which is not explicitly listed as label:
		* e.g. find the ratio of women/man in patient dataset when the gender is not the label
	4. Model Extraction: black-box, want to acquire model
		* sometimes wants to generate a substitiude model, replicate the decision boundary
		* often wants to be efficient, as few queries as possible
		* sometimes also predicting the attributes of the model, the hyper-pram of reg, the optimizer type, the activation type

* Attacks against centralized supervised learning
	* membership attck(shadow training) - lies in that models behave differently when they see data does not belong to the trainig set
![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201125102636.png)
	* reconstruction attack
	* property inference attack
	* Model Extraction attacks:
		* view model param/hyper-parm in objective as the unknown
		* for linear binary classifier with d dimension only needs d+1 queries, for non-linear perceptrons use optimization techniques as BFGS/SGD
		* similar to Active Learning, which has an external oracle to provide label for inquery
		* some others view it as finding the most valuable data to query:
			* use data not synthectic but from other fomain
			* unsupervised techniques as MixMatch

* Attacks against distributed learning
	* federated learning setting, its not safe cause each client could access the model parameters
![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201125103255.png)


* Defenses
* Differentiabel Privacy:
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201125105526.png)
		* add gaussian / laplacian noise to the output as the \epsilon
* other approaches:
	1. regularization
	2. prediction vector tampering(干预)
	3. model compression
	4. ensembel
	5. noisy data
	6. weight quant
	7. selective sharing
	8. membership inference
	
