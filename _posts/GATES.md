# GATES

* 提升基于predictor的NAS
* embedding-将离散的网络架构映射到一个离散空间
* NAS的两个主要模块
  * Searching module - Controller
    * 基于predictor来sample，更好的sample
  * Architecture Evaluation - Predictor
    * 主要问题 - 慢（对于每个架构都要训练好多个epoch）
    * 解决方案
      * shared weights - 一个supernet训练各个部分
        * 加速architecture的evaluation过程
      * predictor是增加sample efficiency（与shared weights对应）(提高sample efficiency也可以用优化controller)
* 早期的encoding基于序列化的方法
  * 人为选择一个方法（规则）把一个图给序列化
  * 而本文使用了GCN的方式（还对gcn进行了一些修改）
    * 传统的GCN无法处理边上带有信息的（边只是表示连接或者不连接）
    * 传统GCN的边的意义是affinity（相似性），edge在NN中是information flow（相连的节点不一定相似），GCN节点上的信息是attribute也就是属性。
      * 有的文章直接把两个节点的embedding拼接起来（并不能保证同构的图能够获得一样的embedding）
      * 本文的目的GATES是去建模NN的information flow，首先将NN建模为一个异构图（包含了数据节点和op节点），information（是一个vector）经过每一个op，会做一个attention，相当于对information加上了一个该op所对应的mask，经过所有op之后最后的那个vector就是该网络的embedding