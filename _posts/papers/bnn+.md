## [Austerity in MCMC Land: Cutting the Metropolis-Hastings Budget](http://proceedings.mlr.press/v32/korattikara14.pdf)

🔑 Key:         

* 提升Bayesian Posterior的 MCMC的sampling效率
* 默认的MH算法中依据N个data point来计算likelyhood function去计算一个binary decision过于computationally inefficient了
* 提出了一种approximate的MH rule (based on sequential hypoyhesis test)
  * likelyhood could be computed from mini-batches of data
  * 只引入了渐进的误差

🎓 Source: 

🌱 Motivation: 

💊 Methodology: 

📐 Exps:

💡 Ideas:  

* 我也看不出做了个啥…总之就是证明了可以用一个subset的数据来近似MH算法中获得likelyhood的过程



## [Beyond Backprop: Online Alternating Minimization with Auxiliary Variables](http://proceedings.mlr.press/v97/choromanska19a/choromanska19a.pdf)

🔑 Key:         

* 原来的一些auxiliary-variable的methods(break the complex nested objective function into local subproblems)基于batch(需要full dataset at each iter)，不是很能够适配extremely large datasets, 或者是continual/Reinforcement Learning的场景
* 本文提出了一种novel的Online的Alternating Optimization方法(且有theoretical convergence的guarantee in the stochastic setting)

🎓 Source: 

🌱 Motivation: 

* 基本和《A Scalable ADMM Approach一样》，用grad-based的方法有一些问题：
  * Local Minima， Objective highly non-convex, non-differentiable nonlinearity, cant parallelize weight update across layers(应该是因为backprop的时候chain rule了所以相互依存?)

💊 Methodology: 

* 并没有用Langrange乘子，only use one set of auxliary variable per layer -> Memory-Efficient as the standard SGD(store the activation value for grad computation)
* ?: 没有太搞清楚它和其他AM的核心区别是什么？

📐 Exps:

💡 Ideas:  



## [Training Neural Networks Without Gradients: A Scalable ADMM Approach](http://arxiv.org/abs/1605.02026)

🔑 Key:       

* Using ADMM to optimize NN instead of Gradient-based methods

🎓 Source: 

🌱 Motivation: 

* SGD训练方式是很多个steps of cheap operation(on a mini batch), however,对于CPU这种multi-core的结构，每一个subtask过于的inexpensive了(所以说更适合GPU)对于parallezing on CPU, 需要small number of expensive minimization steps. 在每一个step去compute the exact gradient on each iteration. 
  * like conjugate gradients, BFGS, Hessian-Free
* 另外stress了grad-based的几个问题:
  * vanishing gradient
  * stuck at local minimal and saddle points
  * not easy to parallelize across layers

💊 Methodology: 

* separating the objective function at each layer into 2 terms:
  * Measuring the distance between weight & Activation （能够让weight的优化不存在Vanishing grad）
  * Containing the nonlinear activation function(non-convex minimization problem that can be solved globally in closed-form)
* decouple the weights from the nonlinear link functions using a splitting technique.
* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210228194418.png)

📐 Exps:

💡 Ideas:  



## [Natural Evolution Strategies](http://ieeexplore.ieee.org/document/4631255/)

🔑 Key:         

🎓 Source: 

🌱 Motivation: 

💊 Methodology: 

📐 Exps:

💡 Ideas:  