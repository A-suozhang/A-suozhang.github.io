## [AutoTrans: Automating Transformer Design via Reinforced Architecture Search](arXiv:2009.02070 [cs])

🔑 Key:    

*  

🎓 Source: 

🌱 Motivation: 

💊 Methodology: 

* Search Space

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210303083620.png)

- LSTM(Controller) - Reinforce； ENAS/NASNet-like flow
- Param Sharing:
  - Cross-operation: 
    - 限制了different num of heads has the same param size
  - Cross-Layer：
    - Albert proves that cross-layer sharing could stabilize training
    - the parameters in the previous transformer layer is shared to the next one.

📐 Exps:

💡 Ideas:  



## [Evolved Speech-Transformer: Applying Neural Architecture Search to End-to-End Automatic Speech Recognition](http://www.isca-speech.org/archive/Interspeech_2020/abstracts/1233.html)
🔑 Key:    

*  

🎓 Source: 

🌱 Motivation: 

💊 Methodology: 

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210303090703.png)

* Search Space (Cell-based)

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210303090116.png)

* Cell-based
  * 感觉它没怎么讲明白它的SS是怎么设计的…有两个branch
* Evo-based Search: Progressive Dynamic Hundles

📐 Exps:

💡 Ideas:  



## [Finding Fast Transformers: One-Shot Neural Architecture Search by Component Composition](http://arxiv.org/abs/2008.06808)

🔑 Key:    

🎓 Source: 

🌱 Motivation: 

💊 Methodology: 

* Search Space：
  1. Attention Key/Query Dimensions
  2. Width/Depth for the feed-forward layers
  3. Num of attention heads
  4. Layer norm mean computation:（一些最近的工作表示不一定需要zero-mean，而是减去w*mean;w是一个可学习的参数）

* 对于每个Component，通过profiling来获得其Cost，component要么是接在上一个component的后(vertical)，要么是并行(Parallel)
  * 对每个component有一个connecting weight
  * 定义了一个connector Connect(f,w)(X,R) = (X+w(f(X)+R), (1-w)(f(X)+R)
  * 维护了一个cumultaive output作为每个connector的输入

![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210303101448.png)

📐 Exps:

💡 Ideas:  

* Smaller models do not always mean faster models:
  * 分析了几种常用的efficient computing的方式，以及FLOPs-oriented的，并不一定能够提升速度
  * Param Sharing: 一般需要更大的model，compute更慢了
  * Pruning/Quantize需要specialized hardware

* vertical feedforward connection and layernorm are relatively more expensive
  * FF as 8 componnets
  * Query-Key as 2 
  * Each head as 1

* 一些架构选择的insight
  * Layer Norm基本没有被选择到，怀疑其有效性
  * key-query similarity in attention can be dropped in earlier layer
  * dk可以减小，但是dv不行
  * FF vertically connected更好



## [Point Transformer](https://arxiv.org/abs/2102.12627)

🔑 Key:      

* 将Transformer引入3d，design了一个PointTransformer的Layer,做了classification & dense prediction task(also as backbone for the 3d scene understanding)

🎓 Source: 

* Oxford & Intel

🌱 Motivation: 

* Transformer将数据看作了一个**set**来描述，与point cloud的intrinsic本身相同(positional information被作为attribute加入)

💊 Methodology: 

1. Vector Attention与标准的Scalar Attention不同，不同于使用一个dot product获得一个值，用了一个relation+mapping的方式去保持dim，本文中用到了subtract作为relation，以及一个MLP作为mapping
2. 其中参与Attention operation的一系列点是local neighborhood points(可能是由KNN所获得的)
3. 对于positional encoding，同样是 MLP(x_i - x_j)
   * empirically found很重要
4. 基础Transformer Block
5. Transition down(downsample) block: reduce the cardinality of the point set.
   * Farthest Point Sampling to find a well-spread point set
   * KNN
   * Linear Transformation(MLP)
   * Max-pool onto each point
6. Transition-Up Task: 对于dense prediction task，需要一个U-Net结构，用一个symmetric的encoder-decoder结构
   * 先过一个Linear T
   * 然后是一个Trilinear Interpolation
   * 然后feature和对应的encoder输出sum起来输出
7. Output Head
   * Seg:
   * Classification: Global Avg Pool

* 这其中整个过程用到都是正常矩阵乘法，所以shape一直是一致的

📐 Exps:

💡 Ideas:  



## [Point Cloud Transformer](http://arxiv.org/abs/2012.09688)

🔑 Key:         

🎓 Source: 

🌱 Motivation: 

💊 Methodology: 

1. Coord-based Input embedding: replacing the original positional encoding 
   * 看起来是2个1x1 conv [3,64], [64,64]
2. A offset-attention module: replacing the original self attention
   * insight from the Laplacian Matrix (offset of degree matrix and adjacent matrix)
   * 改变了atention的方式，由于此处的attention map的shape是[num_point, dims],对1st dim做softmax，2nd dim用L1 norm来归一化(原本的transformer对于[num_token, num_token]的结构，用std标准化1st dim，并且softmax 2nd dim)
3. Neighborhood Embedding module: stress the local geometric info：
   * 参考了PointNet++中的local neighbor aggregation，用两个SG(sample & group)去扩大receptive field
     * 对P个点，先通过Farthest point sampling给downsample到P_s个sample point，再对其中的每个sample point，找knn个点
   * 加在最后的embedding后面（以及possible用在一开始的stem后面）

📐 Exps:

💡 Ideas:  

* Transformer is suitable for point cloud for its inherently permutation invariant



## [SE(3)-Transformers: 3D Roto-Translation Equivariant Attention Networks](http://arxiv.org/abs/2006.10503)
* 提出了一种对SE-3保持equivalence的点云self-attention的方式
  * invariant to input rotation and translation
  * equivariant(同变性) to permutation

🔑 Key:         

* 贴合点云数据特新的Self-Attention方法
* 两个主要部件:像是一张图里的点和边
  1. input-dependent attention-weights (Edges)
  2. embedding of the input

🎓 Source: 

🌱 Motivation: 

* Should be invariant to  global changes(transformation) of the input pose

💊 Methodology: 

* 

📐 Exps:

💡 Ideas:  

* roto-translation equivariant operation for 3d point-cloud

* Tensor filed Network: map point-cloud to point-cloud under constraint of SE3-equvariance