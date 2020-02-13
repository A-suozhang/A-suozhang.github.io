---
layout:     post                    # 使用的布局（不需要改）
title:      Understanding&Debugging PyTorch           # 标题 
subtitle:   火炬心法！        #副标题
date:       2020-02-12             # 时间
author:     tianchen                      # 作者
header-img:  img/11_30/bg-road1.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - Coding
---

> 金鱼记忆的A_suozhang发现自己对一些api以及一些库的用法一段时间不用就会忘记(比如学了几次每次都需要现场查阅的pandas以及matplotlib)
> 所以在这里记录一下一些常用操作的用法作为Reference,其中会有一部分Code Example


# PyTorch

> 对Pytorch的一些内容（比如OrderedDict）还是要多理解机制和封装，不是只是当个Ref来记录（不然我为什么不去查官方文档呢）

### net相关
  * 参考[Pytorch官方文档](https://pytorch.org/docs/stable/nn.html?highlight=nn#module-torch.nn)
  * 标准用法 ```net = Vgg16();```
  * 是一个nn.Module类型的对象
  * 常用操作
    1. net.float()/double()
    2. net.eval()
    3. net.modules() 返回一个module的generator
       * 直接打印迭代器出来的值
       ``` py
       BatchNorm2d(512, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True) 
       Conv2d(512, 512, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), bias=False) 
       BatchNorm2d(512, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True) 
       Conv2d(512, 2048, kernel_size=(1, 1), stride=(1, 1), bias=False) 
       ```  
    4. net.parameters() 返回一个包含了所有参数的
    5. net.forward() 前向传播
    6. net.cpu()/cuda()
    7. net.load_state_dict(d)
    8. d = net.state_dict()
       *  key是module,value是params 
    9. net.to(device)  
    * State_Dict
      * 是一个实例化的net对象的state_dict()方法所返回的
      * keys()是每一层的名字
      * Values()是每一层所对应的Tensor
      * 可以用
       
      ```
      for i,j in enumerate(state_dict):
      print () # j is the keys()
      ``` 
    10. named_xxx()系列返回一个Generator
  
    ``` py
    for int,tuple in enumerate(net.named_parameters()):
        # int 是index
        # Tuple第一个元素是一个string, 后一个是Parameter形的
        # layer4.1.conv1.weight <class 'torch.nn.parameter.Parameter'>

    ```
    11. 如何操作net
      * **如何解构一个net?**
        * *官方给的mode一般都像套娃一样*
        * 首先net.children() net.modules()这些东西都是generator
        * 我们可以直接用一个list(net.childern)一下把它的东西给全部包起来
          * 然后用len()或者是index直接索引来看
          * *活用type和pytorch官方文档了解模块的封装形式*
      * 我们发现net.children()中的元素是一个tuple
        * 第一个元素是名字
        * 第二个元素就是torch.Sequential了 
      * Sequential是一个小婊砸:
        * 一开始我被难住了,后来发现它可以当做list用,直接索引
      * 下一级就是这样的一个具体的block了
       
      ```
      InvertedResidual(
          (conv): Sequential(
            (0): ConvBNReLU(
              (0): Conv2d(32, 32, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), groups=32, bias=False)
              (1): BatchNorm2d(32, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
              (2): ReLU6(inplace=True)
            )
            (1): Conv2d(32, 16, kernel_size=(1, 1), stride=(1, 1), bias=False)
            (2): BatchNorm2d(16, eps=1e-05, momentum=0.1, affine=True, track_running_stats=True)
          )
        )
      ``` 

        * 对这种block直接按照里面的名字索引,比如这里```xxx.conv``` 
      * 这样抽丝剥茧总能到一个具体的Function
        * 比如```Conv2d(32, 32, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1), groups=32, bias=False)```
        * 对于这个具体的Function
          * xxx.weight就是一个nn.parameter
      * 对这个Parameter,的.data就是一个Tensor
      * 对parameter做对应的操作(比如对不同层选取不同的lr)

      ``` py
      param_group = []
      for k, v in net.named_parameters():
                  if not k.__contains__('fc'):
                      param_group += [{'params': v, 'lr': learning_rate}]
                  else:
                      param_group += [{'params': v, 'lr': learning_rate * 10}]
      ```
	* load_state_dict(strict=False)可以忍受Missing Keys


### Datasets
   * 这里是直接调用了官方提供给*特定数据集*的Loader方式
     * ~~也可以采用[官方示例中的读取方式](https://github.com/bearpaw/pytorch-classification/blob/master/imagenet.py)~~
       * 对不起上面那个只是民间复现,[这才是官方范例](https://github.com/pytorch/examples/blob/master/imagenet/main.py)
       * ~~(但是他们读取数据的方法是一致的)~~
     * 但是也可以用torchvison.datasets.ImageFolder($DIR, xxx,xxx)
     * 是直接按照默认方式进行读取(读取一个文件夹下的所有东西,直接按照下一级的文件夹名字作为分类标准)
       * trainset.classs_to_idx 自动分配了对应的类别,都是对应的list
        
   ``` py
   trainset = torchvision.datasets.CIFAR10(root='./data', train=True, download=False, transform=transform_train)
   trainloader = torch.utils.data.DataLoader(trainset, batch_size=128, shuffle=True, num_workers=2)
   ``` 
   * ImageFolder类型的Dataset
     * 把图片按照类存在一个文件夹下,相当于有了Label
     * dataset.samples,可以来索引数据
     * dataset.classes
   * DataLoader内部还会有一个DataSampler来提供取Dataset的Indice
     * 分为Sampler和BatchSampler（在dataloader定义时候作为arg输入，默认都为False）
       * Sampler用于产生indices
       * BatchSampler则是将indices打包为Batch
     * Pytorch官方实现了Random，Sequential， Weighted，SubsetRandom
     * 纯默认情况，如果shuffle = True，就用Random，否则用Sequential
     * 如果自己定义了BatchSampler，shuffle需要设置成false
   * SubsetRandomSampler - Sampler的一种
     * 从输入的indice list中随机抽取
     * replacement不满足(同一个indice不会被重复输出)
   * BatchSample(sampler, batch_size, drop_last = True)
     * 基于一个sampler依据BatchSize进行打包

   *　[PyTorch torchvision.transforms - 胡今朝的文章 - 知乎](https://zhuanlan.zhihu.com/p/52325016)
   *  [Official Doc](https://pytorch.org/docs/stable/torchvision/transforms.html?highlight=torchvision%20transforms )
   *  有关transform的example写在```DataAug.ipynb```中
      *    
   * 需要经过一个* [Transform(From Torchvision)](https://pytorch.org/docs/stable/torchvision/transforms.html?highlight=transform)，不仅做了一些对去均值之类的操作，而且是将原本的PIL图像转化为tensor
   ``` python
   transform_train = transforms.Compose([
      transforms.RandomCrop(32, padding=4),
      transforms.RandomHorizontalFlip(),
      transforms.ToTensor(),
      transforms.Normalize((0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010)),
   ])
   ```
    * 
    * Pytorch原生的Transform针对的是从图片中读取出来的PIL对象
      * 如果输入已经是一个Tensor再做Transform会报错 ```TypeError: cannot unpack non-iterable builtin_function_or_method object```
      * 同理输入是Numpy的话也需要基于Numpy做操作
      * PIL和能够直接plt.imshow()的对象都是[32,32,3]这样的,而从Tensor过来的一般都是[3,32,32],所以transforms的ToTensor方法默认安排了一个Permute操作
    
    ```
    n_out = np.random.rand(100,100,3)
    t_out = transforms.ToTensor()(n_out)
    img2 = transforms.ToPILImage()(t_out.float())  #强制类型转换
    ```

    * 这边初始数据一定要Crop一下,否则会报错 ```RuntimeError: invalid argument 0: Sizes of tensors must match except in dimension 0. Got 375 and 500 in dimension 2 at /opt/conda/conda-bld/pytorch_1570910687650/work/aten/src/TH/generic/THTensor.cpp:689```
      * 参考[这里](https://github.com/marvis/pytorch-yolo2/issues/89)
   * ```next(iter(trainloader{是一个pytorch当中的DataLoader}))```返回一个tuple，第一个元素为Data，是一个图。第二个元素为Label，是一个数
   * 获取Batch内部的数据

   ``` python
   def getBatch():
      for batch_idx, (input, target) in enumerate(trainloader):
         yield input # 构造了一个生成器
   batch_data = next(getBatch())
   # 返回的是一个Tensor [Batch_size,C,W,H]
   ```

  * 或者直接 
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191014154232.png)
      ```
      it = iter(train_loader)
      image, label = it.next()
      ``` 

  * 数据集是什么样的类型:
    * ```trainset = torchvision.datasets.CIFAR10(root='./data', train=True, download=True, transform=transform_train)```
    * trainset的形式:
      * ```Dataset CIFAR10
            Number of datapoints: 50000
            Root location: ./data
            Split: Train
        ```
      * train.set.data是一个numpy array(因为我没有apply transform中将其tensor化```transforms.toTensor()```)
        * shape为[50000,32,32,3]
      * trainset.class_to_index各个类别的对应label
    * 本质上是继承了```torch.utils.data.DataSet```
      * 实现了getitem和len方法
      * 所以可以被```torch.utils.data.DataLoader```
        * 使用了```torch.multiprocessing```来多线程读取

### Layers

* 手写一个自己的层
  * 继承nn.functional 
    * 实现其foraward和backward函数(需要指定为static方法)
  * 继承nn.module模块

* [官方tutorial](https://pytorch.org/docs/stable/notes/extending.html)
* 一个我的实现例子


``` py

from torch.nn.parameter import Parameter
from torch.nn import init
from torch.autograd import Variable
from torch.autograd import Function
import torch.nn as nn
import torch
import ipdb

class MyBN_f(Function):

    @staticmethod
    def forward(ctx, inputs, running_mean, running_var, weight, bias, is_training ,momemtum, eps, returned_mean, returned_var, returned_whiten_inputs):

        if is_training:
            print("Is_Training")
            mean = torch.mean(inputs, dim = [0,2,3])
            var = torch.var(inputs, unbiased = False, dim = [0,2,3])
            var = torch.mean((inputs - mean.reshape([1,-1,1,1]))**2, dim=[0,2,3])
            # N = inputs.shape[0]*inputs.shape[2]*inputs.shape[3]
            returned_mean.copy_(mean)
            returned_var.copy_(var)

            # Replace The Running Mean/Var Inplace So We Don't Need To Return Them
            running_mean.copy_((1-momemtum)*running_mean + momemtum*mean)
            running_var.copy_((1-momemtum)*running_var + momemtum*var)

            squared_var = torch.sqrt(var + eps).reshape([1,-1,1,1]) # Prepare For the Backward

        else:
            mean = running_mean
            var = running_var
        
        # The Main Body
        mean = mean.reshape([1,-1,1,1])
        var = var.reshape([1,-1,1,1])
        
        whitened_inputs = (inputs - mean) / \
            squared_var
        returned_whiten_inputs.copy_(whitened_inputs) # Save The Whiten Input

        output = whitened_inputs*weight.reshape([1,-1,1,1]) + bias.reshape([1,-1,1,1])
        
        ctx.save_for_backward(whitened_inputs, mean, squared_var, weight, \
                              torch.tensor(is_training,device = inputs.device))

        return output

    @staticmethod
    def backward(ctx, grad_y):
        N = grad_y.shape[0]*grad_y.shape[2]*grad_y.shape[3]
        whitened_inputs, mean, squared_var, weight, is_training = ctx.saved_tensors


        # The Main Body
        g_w = torch.sum(grad_y*whitened_inputs, dim = [0,2,3])
        g_b = torch.sum(grad_y, (0,2,3))
        if is_training.item():
            g_whitened_x = torch.mul(grad_y, weight.reshape([1,-1,1,1]))
            g_var = torch.sum(g_whitened_x *(whitened_inputs) * (squared_var.pow(-2)) * (-0.5), dim = [0,2,3]) # The grad for Var not Squared Var
            g_mean = torch.sum(g_whitened_x * squared_var.pow(-1) * (-1),dim = [0,2,3])


            ipdb.set_trace()
            grad_x = (1/(N*squared_var))*(N*g_whitened_x - torch.sum(g_whitened_x, dim = [0,2,3]) \
                                            - whitened_inputs* torch.sum(g_whitened_x*whitened_inputs, dim = [0,2,3]).reshape([1,-1,1,1]) )
            # grad_x = g_whitened_x*squared_var.pow(-1) + ((1/N)*g_mean).reshape([1,-1,1,1])
                # + (whitened_inputs)*(squared_var)*(2/N)*(g_var.reshape([1,-1,1,1]))
        else:
            grad_x = grad_y * weight.reshape([1,-1,1,1]) * squared_var.pow(-1)

        return grad_x, None, None, g_w, g_b, None, None, None, g_mean, g_var, g_whitened_x








class MyBN(nn.Module):
    def __init__(self, num_features, eps = 1e-5, momemtum = 0.1, affine = True, track_running_stats = True):
        super(MyBN, self).__init__()
        self.num_features = num_features
        self.eps = eps
        self.momemtum = momemtum
        self.track_running_stats = track_running_stats
        self.affine = affine
        # self.mean = Variable(torch.Tensor(num_features))
        # self.var = Variable(torch.Tensor(num_features))
        self.mean = torch.Tensor(num_features).requires_grad_()
        self.var = torch.Tensor(num_features).requires_grad_()


        if self.affine:
            self.weight = Parameter(torch.Tensor(num_features))
            self.bias = Parameter(torch.Tensor(num_features))

        if self.track_running_stats:
            self.register_buffer('running_mean', torch.zeros(num_features))
            self.register_buffer('running_var', torch.zeros(num_features))
            self.register_buffer('num_batches_tracked', torch.zeros(num_features, dtype = torch.long))
        self.reset_parameters()

    def reset_running_stats(self):
        if self.track_running_stats:
            self.running_mean.zero_()
            self.running_var.fill_(1)
            self.num_batches_tracked.zero_()

    def reset_parameters(self):
        self.reset_running_stats()
        if self.affine:
            init.ones_(self.weight)
            init.zeros_(self.bias)

    def _check_input_dim(self, input):
        if (input.dim() != 4):
            raise ValueError("The Input Should Be in [Num_Batch, Channel, W, H]")

    def forward(self, inputs):
        self._check_input_dim(inputs)

        self.whitened_inputs = torch.Tensor(inputs.size())
        self.whitened_inputs.requires_grad_()

        return MyBN_f.apply(inputs, self.running_mean, self.running_var, self.weight, self.bias, \
                            self.training , self.momemtum, self.eps, self.mean, self.var, self.whitened_inputs)

```

* 如果自己实现了function.在module的前向的时候要调用Myfun.apply()这样
* backward的返回参数列表对应着forward的输入参数
* backward和forward之间通过 ```ctx.save_for_backward``` 以及 ```ctx.saved_tensors``` 来传递与共享参数
* 我的实现中为了保持forward返回值的简介,又想把计算的中间结果赋值给到module中的self的attr,于是在前向时候传了self.XXX进去,再在forward的过程中用copy_() inplace的改变内部的值,以达成传一个指针进去的效果 
* NN.module的self对象,需要register成buffer或者是parameter才是严格的module一部分


---

* Instacne Normalization  
  * 均值和方差是per dimension进行统计的
  * 第一个参数 Num_Features一般和Channel数目一样
  * eps是为了保持Numerical Stability加的一个很小的数字,默认1e-5
  * track_running_stats是维护一个当前Set的均值方差的滑动平均,momentum参数则表示内部的系数
    * 如果设置为False的话不管是train还是eval都会采用当前batch的均值方差(*在这里默认是False*)
  * 当affine设置为True的时候会有一个可以学习的参数wx+b (*但是这里默认为False*)
  * 输入输出的尺度是一致的
  * *IN可以被认为是只对应一张图片的*
* nn.Sequential(nn.Conv2d, nn.Linear)
  * 比如说我不想改变网络的forward函数,而想增加一个fc层(比如对固定好的res50)可把原来的net.fc替换成一个两个fc的SequentialModel 
  * 当然Sequential里面也可以塞一个OrderedDict
    * 这样Sequential对象里面的模块也可有名字
* 调用的是torch.nn中的Conv2d函数(这个返回的是一个nn.functional和nn.functional.conv2d等价)
  * 输入args
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191112185933.png)
    * inplane/outplane 输入输出Channel数目,很直观
    * Kernel_size
    * Stride (默认为1))
    * padding (默认为0)
    * bias(默认为True)这里设置为None    
      * bias是直接加在每个Output Channel上的
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191112185239.png)       
    * groups(描述了input和output之间的Connection)这里设置为了1
      * inplane和outplane要都能被groups整除
      * groups = 1 - 正常的卷积
      * groups = 2 -  相当于有两个卷积层,每个处理一半的inputchannel,产生一般的outputchannel,最后再Concat起来 (*相当于切断了一半的in_channel之间的联系*)
      * groups = in_channel - 相当于每个in_channl都和(OUTC/INC)个卷积和去卷,最后输出的结果全部concat起来
    * dilation - 默认为1
      * [图示](https://github.com/vdumoulin/conv_arithmetic/blob/master/README.md)
    * *dilation和stride之类的东西都可以是一个int或者是一个tuple*
      * 可以用来描述横纵不一样时候的情况
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191112185933.png)


### Optimizer
```optim.SGD(net.parameters(), lr=args.lr, momentum=0.9, weight_decay=5e-4)```
* optim的第一个输入参数是一个list(也就是```net.parameters()```返回的)
  * 里面的元素是一个dict
  * 默认的list长度是1
  * **当你需要对于每一层不同的lr的时候,可以手动改这个list,把它变长,然后dict里面的lr取不同的值**
* 所有的optimizer都会实现一个step()方法，利用它来*实现参数的更新*（前提是之前执行过计算图的```loss.backward()```）
* loss的两个输入参数形式是（可能会有long tensor的要求）

``` py
input = torch.Tensor([[0.1,0.8,0.1],[0.4,0.6,0]])
 # 3 Class, 2 Batch_size
target = torch.Tensor([1,1])
```

---

* 分段的LR
    * 查看LR
      * ```optimizer.param_groups[0]['lr']```
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191027195406.png)
    * [torch.optim.lr_scheduler.MultiStepLR(optimizer, milestones, gamma=0.1, last_epoch=-1)](https://pytorch.org/docs/stable/optim.html?highlight=lr_scheduler#torch.optim.lr_scheduler.MultiStepLR)

    ``` py
    >>> # Assuming optimizer uses lr = 0.05 for all groups
    >>> # lr = 0.05     if epoch < 30
    >>> # lr = 0.005    if 30 <= epoch < 80
    >>> # lr = 0.0005   if epoch >= 80
    >>> scheduler = MultiStepLR(optimizer, milestones=[30,80], gamma=0.1)
    >>> for epoch in range(100):
    >>>     train(...)
    >>>     validate(...)
    >>>     scheduler.step()
    ``` 

    * 在执行一次scheduler.step()之后，epoch会加1，因此scheduler.step()**要放在epoch的for循环**当中执行。

* WarmUp的LR
  * 官方没有给,所以需要我们**自己写**
  ``` py

  from torch.optim.lr_scheduler import _LRScheduler

  class WarmUpLR(_LRScheduler):
    """warmup_training learning rate scheduler
    Args:
        optimizer: optimzier(e.g. SGD)
        total_iters: totoal_iters of warmup phase
    """
    def __init__(self, optimizer, total_iters, last_epoch=-1):
        
        self.total_iters = total_iters
        super().__init__(optimizer, last_epoch)

    def get_lr(self):
        """we will use the first m batches, and set the learning
        rate to base_lr * m / total_iters
        """
        return [base_lr * self.last_epoch / (self.total_iters + 1e-8) for base_lr in self.base_lrs]
  ``` 
  * 继承官方的_LRScheduler  
    * 需要实现一个get_lr方法吧我们需要的lr写在里面
    * self.last_epoch表示上一个epoch数



* optimizer.param_groups是一个list
  * 其[0]是一个dict
    * keys - ```dict_keys(['params', 'lr', 'momentum', 'dampening', 'weight_decay', 'nesterov'])```
      * param是一个list，里面是Tensor


### [Tensor](https://pytorch.org/docs/stable/tensors.html)

* 直接赋值其实两个变量是指向同一个内存地址的
  * 需要两个一样值的，用 Variable(x.data.clone())
  * clone是**深拷贝**,而一般的赋值传的是**引用**
* 而copy和"="的区别其实在Python中
  * 前者是建立新对象,后者是传一个引用
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191207191020.png)

#### 尺度变化

* **想要使用尺度变化,首先要清楚计算的Broadcast原理**
  * *本质,想象为一个先拓展到和另外一个一样大,再做逐点乘*
  * 成立条件: 每个对应维度大小要么相同,要么其中有**一个是1**,要么其中有一个不存在
  * 如果x,y的维度不同,会优先给空着的维度补0 ```[3,2,4,4]*[2,4,1]``` 
    * 注意在这里,最低维y为1,x为4,可以,但是如果y的最低维为2就不行
  * 对于每个维度.计算结果是xy中较大的那个
* 尽量避免使用Permute操作,比较费时间,如果是为了做broadcast还是使用更加直接的方式
  * 一个比较好的例子: BN的mean是[c]但是输入参数是[N,c,w,h]我们需要将mean broadcast过去
    * 将mean reshape为[1,c,1,1],再做加减法 ```mean.reshape([1,-1,1,1])```
    * 另外一种方式是将其拓展为 [c,1,1] ,然后再做 ```mean[:,None,None]```
      * **这种做法必须向后对齐,很神奇**
* 当我想制作一个fake data的时候 ```[Batch, Channel, W, H]```
  * 拓展数据,使用```tensor.expand([1,1,3,3])``` 
  * 或者是使用```torch.stack([x,x,x], dim = 0)```
    * stack会直接新建一个维度,而cat则不会
    * 比如```[1,2,2]```做一个```torch.stack([x,x], dim = 2)``` 
      * stack ```[1,2,2,2]```
      * cat  ```[1,2,4]```
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191207200443.png)
* 对于一个正常的Tensor,需要做转置的时候用```x.T```,但是如果这个tensor只有1维度,比如```torch.tensor([1.,2.,3.])```,对其做T得到的shape和原来一样
	* 这时候就可以用```x[:,None]```
* 对于想要在For-loop中将一些Tensor给concat起来
	* 可以先利用一个List将需要concat的存放,然后用```torch.stack(list, dim=0)```进行拼接
	* 生成这个list可以用list comprehension来做



#### Autograd
* 注意**在0.4.0之后Tensor默认就是Variable，所以不要出现```Variabe(torch.tensor(3))```这样的写法，甚至可能会重置该tensor的grad**
  * 但是注意```require_grad```还是需要加的，定义input的时候将其grad设置为True
* 注意对Scalar对象进行backward的时候
  * retain graph = True才能反复backward
* 默认只有Leaf Variable有grad（也就是除了x-也就是源头之外所有中间结果的grad都没有存下来）
  * 不会报错但是对所有的Leaf Variable，都有require—grad True, 但是grad为None
  * 可以对一个对象进行retain_grad()方法来解决 (在backward之前)
* 声明Tensor的时候默认的require_grad是False哦 ```x = torch.tensor([[1., -1.], [1., 1.]], requires_grad=True)```
    * 用torch.tensor来声明Tensor，torch.Tensor是一个Class
* 经过计算backward之后，每个tensor的梯度被存在Tensor.grad内部
    * *只有是item单个数目之后的结果才可以做backward哦-很合理这样才是一个计算图*
* torch.apply_(callable) - 对Tensor里的每一个元素执行一样的操作
    * *只能被用在CPU Tensor上而且不能用于高性能需求的时候*

* 可视化(Tensorboard被包含到了torch里面)
    * ```from torch.utils.tensorboard import SummaryWriter```
    * [官方给的Tensorboard的Tutorial](https://pytorch.org/tutorials/intermediate/tensorboard_tutorial.html)

### S/L
* 存储
  * torch.save(dict, 'xxx.pth')
* 读取
  * 读取的是'.pth',内部是一个dict,keys有net,acc,epoch
    * 这个里面的内容是自己打包的,可以选择
  * 读取pth出来的net是一个OrderedDict



### Tensorboard
* [一个教程](https://www.pytorchtutorial.com/pytorch-builtin-tensorboard/)
* ~~```from torch.utils.tensorboard import SummaryWriter```~~
  * 官方给的功能没有
* add_scalar(tag, scalar_value, global_step=None, walltime=None)
  * tag值可以手动设置为```section/plot```可以分为不同的scope

### Multi-GPU

> 参考了[Pytorch多机多卡分布式训练 - 谜一样的男子的文章 - 知乎](https://zhuanlan.zhihu.com/p/68717029)

* DataParallel的batchsize需要设置为单卡情况的4倍,而分布式的Distributed不用
* 单纯的DataParallel的wrapper是浅复制model到所有可用的显卡中
* 在代码中将数据丢到多卡的第一张(controler)上
* 实际运行的时候每个batch被**平分**到多张卡上
  * 最后的Loss和G都x4了(当然在求的时候也平均了,所以相当于就是用了一个大Batch)

# Some Resoueces

* [Awesome-Pytorch-List资料库](https://github.com/bharathgs/Awesome-pytorch-list)
* [跟着官方学下net应该咋子写](https://github.com/pytorch/vision/blob/master/torchvision/models/resnet.py#L38)
    * 继承```nn.Module```的模块成Block类，init的时候一般需要调用父类的构造函数```super(BasicBlock,self).__init__()```
* 有2个很好的train的example，已经fork到workspace当中了
    * [Cifar10](https://github.com/kuangliu/pytorch-cifar)
    * [Cifar100](https://github.com/weiaicunzai/pytorch-cifar100)

# Related Stuff
* [ONNX](https://onnx.ai/)（Open NN Exchange Format开放式神经网络交换）-模型交换格式工具
* JIT(Just In-Time Compilation)即时编译
    * 混合了静态和动态编译
    * 与C++相联系转化的时候会用到
* REPL - 交互式命令行
    * Ipython属于
* fastai-超快速上手的DL工具
    > Make Deep Learning Uncool
    * [Stuffs About It](https://github.com/zergtant/pytorch-handbook/blob/master/chapter4/4.3-fastai.ipynb)
* CLI-Command Line Level
* LMDB (lightning-Memory-Mapped DataBases)内存映射数据库
  * 内部一个数据文件,一个锁文件
  * 将所有图片存在一个文件里面,可以减少机械硬盘的读取时间(IO开销)
    * 可以*提高磁盘利用率*
  * 用*内存映射的方式存储文件*
  * 不需要额外的数据库进程
* hd5会存储每个像素的值,会超级大
  * 对tf有tfrecords格式去压缩数据
* ImageNet
  * 全部的数据集有1.25M,2w多类,1T左右
  * 我们一般用到的是ILVSRC比赛的,大概160G不到一点
    * Train - 每个类别1k多张 1281167
    * Val - 5W - 1000x50
    * Test - (比赛中不公开label)
  * 文件结构
    * train.txt Test.txt 包含了对应的lable信息
* Cifar100-文件大小同cifar10 (32x32x3)
  * 50000张
* ctx (出现在尝试重写nn.function的时候,在forward的时候调用了ctx.save_for_backward)
  * 包含了function运行时候的存储参数
  * 可以和class中的self来类比 
  * 和staticmethod搭配使用,由于使用staticmethod的时候,是直接利用的classtype,而不是一个class的instance(所以不用self)
* 所谓的MetaClass,继承Type类,也就是它的instance是和int/float同级别的
  * 通过修改它的__new__方法,做了一个register的操作
    * 每当一个基类创建对象的时候都首先会调用MetaClass的New方法(相当于Type的构造方法))
  * 鉴别方式```isinstance(module.__class__, FixMeta)```
    * module是一个具体类的instance,它的class是具体类,而具体类如果是一个FixMeta
    * isinstance(module, Conv2d) 
    * module本身是一个conv0
  * 官方有一个ABC库做了类似的事情



# 炼丹心得
* 按照默认的参数配置发现需要比*想象得多的Epoch*才能获得一个比较好的模型，虽然当前Loss基本上不往下走了，还是要训它
  * 普遍认知基本上都要在数据集上走100个epoch
* 非常经典的参数配置
 
|||
|--|--|
|Batch_Size|128|
|WeightDecay|5e-4/4e-5|
|lr|0.1(Default))|
|MultiStepLR|[60,120,160] by 0.2/ [150,250] by 0.1|

* Warm-Up Tranining
  * 在训练的一开始（前10Epoch）拿比较小的lr先开始训练，然后逐渐增大到正常的LR(比如0.1)
* 加大了Batch-Size的话对于同样的Epoch数，拟合效果会更好，如果采用默认配置有可能会Overfitting

* 当显存不够的时候(不是现在)导致Batch-Size上不去，有一个奇技淫巧（其实现在已经是常规操作了，叫Accumulating Gradients）
  * 模拟大Batch-Size
  * 做多次前向和一次反向，这样模拟出16xBatchSize

  ``` py
  # clear last step
  optimizer.zero_grad()

  # 16 accumulated gradient steps
  scaled_loss = 0
  for accumulated_step_i in range(16):
       out = model.forward()
       loss = some_loss(out,y)    
       loss.backward()
        scaled_loss += loss.item()
        
  # update weights after 8 steps. effective batch = 8*16
  optimizer.step()

  # loss is now scaled up by the number of accumulated batches
  actual_loss = scaled_loss / 16
  ``` 

* 正确的复制方式，用item()，否则会多保留一份copy

```
# bad
losses.append(loss)
# good
losses.append(loss.item())
```

* 多卡的操作方式    
  * 查看多卡的信息 ```torch.cuda.get_device_name(i)```

``` py
model = resnet18()
model = nn.DataParallel(model, device_ids=device_ids)
result = model(input)
```

* 下午炼了好久以为自己用的是res50其实是EfficientNet,,按照文章里参数吧BatchSize整贼大,然后效果不好,换会Res50一看,256的BatchSize就把显存拉满炼
  * ~~网络的体量差距好大啊~~
  * 另外还深刻感受到了BatchSize不能太大,太大了在第一次LR Decay之后就是肉眼可见的过拟合,而且前期震荡

* **BatchNorm的正确用法**
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191102194152.png)
  * 就是它,有这样的几个输入参数
      * Num_features - 和输入数据的Channel数目保持一致
      * eps - 保持数值稳定的一个很小的数,我们不care
      * keep_tracking_stats - 如果设置为True的话,就保存下当前的一个个Batch的均值方差的一个Runing Mean
      * Affine 有没有一个可以learn的wx+b (默认为True)
  * **非常关键!!**
    * Batchnorm中的均值和方差到底是什么东西呢?
    * 首先,在完全默认的情况下,momentum=0.1,keep_tracking_stat = True
    * 在net.train()的时候
      * **虽然在keep_tracking_stats = True**的时候,记录下了这个滑动平均
      * 但是**在训练阶段前向传播的时候这个平均值并没有被用到**,实际用的是**每个MiniBatch的均值方差**
    * 而在net.eval()的时候
      * 使用的是之前记录下来的滑动平均了,均值方差不会再被更新
      * 当然如果又是eval又*keep_tracking_stats = False*的话,eval的时候也是不会使用一个滑动平均的(因为完全没有被记录下来desu)
*  对于数据集的transform,现在是不是可以不加Normalize,而是在网络的一开始加一个Instance Normalization~~
   *  因为本身Instance Norm就是对每个Channel做的,而且是每张图做的
   *  (还不是特别确定)
* Batchsize大了效果会好
* lr与batch的大小成正比


### Distributed Training

> 多卡中单纯使用DataParallel会导致很慢，GPU利用率低

* 执行指令加前缀```CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 python -m torch.distributed.launch --nproc_per_node 8 main.py```
* 以上指令会给代码输入一个叫```--local-rank```的arg，代表这是第几个process，一般一卡一个
  * 如果没有用distributed，会是-1
  * 然后从0，1，2，3
* 需要两行代码来启动distributed
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211113845.png)
* 设置gpus
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211113952.png)
* Dataloader的Sampler需要对应着改变
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211114030.png)
  * Testloader不需要用distributed，不然会给出多个test结果
* 还有一些小的地方需要处理
  * 比如打印和保存，应该只在0号上进行，不然会向控制台打印多份
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200211114316.png)

* [还有一个教程没看](https://zhuanlan.zhihu.com/p/98535650)



# Troubleshooting!
* 2019-10-24-eve: 在ImageNet预处理中,把第一步的RandomSizeCrop(224)写成了RandomCrop(224),导致整体崩溃了
  * 出bug的地方在trainloader Load数据的时候,报了一个```ValueError: empty range for randrange() (0,-23, -23)```
  * ~~最尼玛骚的是这个在num_of_works < batch_size的时候不会被触发,太迷了!!~~
* 在使用tensorboard尝试构建graph的时候
  * Cannot insert a Tensor that requires grad as a constant. Consider making it a parameter or input, or detaching the gradient
  * 悬而未决
* Tensor往gpu传的时候不能做到inplace
  * 对于model可以用```model.to(device)```就可以了
  * 但是tensor必须```t = t.to(device)```
    * 不支持inplace
  * 如果没有操作对的话就会报```RuntimeError```
* ```Unexpected key(s) in state_dict: "module.conv1.weight", "module.bn1.weight", "module.bn1.bias"```
  * 是因为训练保存模型的时候用了DataParallel与读取的时候不一致了,可以在读取的时候加上
  * *模型保存的时候和卡的张数和device_id都有关系有些不合理*
* 注意如果```device='cuda'```的话默认是用0卡的相当于```cuda:0```如果要用其他卡作为主卡,则需要让```device:2```,之后再XXX.to(device)
  * 注意net多卡时候的DataParallel中的device_ids,第一个最好设置为主卡
* logits再归一化的结果是softmax
  * logits是在概率上又套了一个log
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191103120554.png)
* pytorch原生dataloader的shuffle是不是每次从dataset中取数据也是随机的?
  * 比如我每次都固定iter一半的dataloader的长度,是不是总是在对数据集的前一半分析呢?
* 一个比较hacky的方式来实现 *Infinite Dataloader*
  * 正确的方法是使用原生的*IterableDataSet*,但是我这里使用了Salad库,封装在里面,不是很方便重构
  * 我们的操作方式是,检测StopIteration异常的出现,如果异常了,就重置一个iter
   
  ``` py

  try:
      input_t0, input_t1, label_t1 = it_t.next()
  except StopIteration:
      it_t = iter(digitloader.datasets[1])
      input_t0, input_t1, label_t1 = it_t.next()
  input_s, target_s = it_s.next()

  ```

* 在我的代码里使用inplace的方法```tensor.mul_()```和使用赋值的方法差距很大,原因不是那么知道!
	* inplace 和 ```x = ...```
	* ! Inplace的替换和赋值有区别,比如说都是对bn.weight赋值,用第二种方式相当于将bn层的weight指向了一个新的tensor,但是我们一开始加到optimizer中的还是原来那个Tensor,导致了新的参数没有被更新
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200102224419.png)
	* **注意inplace修改和替代的区别!这个坑经常会遇到!**
* 由于我在整个代码的最后加了一个writer.add_scalar但是没有运行到那里就停了,导致认为是writer没有close,tensorboard里没有数据
* 使用多卡的时候出现bug,检查一下是否先```to(device)```再```Data Parallel```
* 突然出现loss直接炸裂的情况,在每个batch_iter,都需要先将optimizer做```zero_grad```,再进行train,再去做step
  * 由于Pytorch的每一步操作都是独立的,所以backward的时候并没有清零梯度,backward默认会让梯度累加
  * 也就是按照模板
  ``` py

  optimizer.zero_grad()             ## 梯度清零
  preds = model(inputs)             ## inference
  loss = criterion(preds, targets)  ## 求解loss
  loss.backward()                   ## 反向传播求解梯度
  optimizer.step()                  ## 更新权重参数

  ```
* 终于搞清楚了Tensorboard的那个Bug:有的时候运行的时候会出现```Failed To Unpack byte of 4```
  * 应该是当开着该目录下的tensorboard,同时**该目录下的某个任务被CTRL+C终止了,或者是新的开始了?**,这样会导致写坏一个eventOut
  * 修复办法: 不要这么做
* ```AttributeError: ‘DataParallel’ object has no attribute ‘xxxx’```
  * 需要通过DataParallel.module调用原来的网络
  * 比如```net.module.set_fix_method()```
* 查看Tensor的type
  * tensor.dtype
* ``` result type Float can't be cast to the desired output type Long```
  * state_dict里面的BN层的Num_batched_tracked是个int64(也就是long型)
* 当出现莫名其妙的```No Module Named XXX```的时候怀疑一下是不是自己命名的时候文件名和内置库的名字冲突了(比如pdb.py)
* 对于一个(300,)的tenso，其实本质上是一维的，第二维度可以是任意值，和(300,1)有本质的区别 
* ```super(type, obj) obj must be an instance``` 可能是ipython reload模块导致的,需要重新更新
* jupyter notebok和argparse不匹配,只需要在最后```args = parser.parse_args()```改成````args = parser.parse_args(args = [])```
  * 但是问题是如果这样的话就不能看help,调用--help会直接开始按照默认参数跑(看上去无伤大雅先这样把)
* Random Seed

  ```python
  if args.manualSeed is None:
      args.manualSeed = random.randint(1, 10000)
  random.seed(args.manualSeed)
  torch.manual_seed(args.manualSeed)
  if args.use_cuda:
      torch.cuda.manual_seed_all(args.manualSeed)
  ```
* 有时Control-C中止运行后GPU存储没有及时释放，需要手动清空 ```torch.cuda.empty_cache()```
  * 或者kill进程 ```ps aux | grep python; kill -9 [pid]```
  * 最不济强制```nvidia-smi --gpu-reset -i [gpu_id]```
* 于PIL转化

``` py
# torch.Tensor -> PIL.Image.
image = PIL.Image.fromarray(torch.clamp(tensor * 255, min=0, max=255
    ).byte().permute(1, 2, 0).cpu().numpy())
image = torchvision.transforms.functional.to_pil_image(tensor)  # Equivalently way

# PIL.Image -> torch.Tensor.
tensor = torch.from_numpy(np.asarray(PIL.Image.open(path))
    ).permute(2, 0, 1).float() / 255
tensor = torchvision.transforms.functional.to_tensor(PIL.Image.open(path)) 
```

---

* [一些常用操作](https://zhuanlan.zhihu.com/p/59205847)
* 初始化网络 ```init.xavier_uniform(net1[0].weight)  ```
  * 或者手动用numpy做

  ``` py
  for layer in net1.modules():
    if isinstance(layer, nn.Linear): # 判断是否是线性层
        param_shape = layer.weight.shape
        layer.weight.data = torch.from_numpy(np.random.normal(0, 0.5, size=param_shape)) 
        # 定义为均值为 0，方差为 0.5 的正态分布
  ```


