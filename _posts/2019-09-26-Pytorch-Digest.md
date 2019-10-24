---
layout:     post                    # 使用的布局（不需要改）
title:      Understanding&Debugging PyTorch           # 标题 
subtitle:   Also A Bit About Python        #副标题
date:       2019-10-23              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-nmb-corner.jpg  #这篇文章标题背景图片  
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


### 读取数据集
   * 这里是直接调用了官方提供给*特定数据集*的Loader方式
     * 也可以采用[官方示例中的读取方式](https://github.com/bearpaw/pytorch-classification/blob/master/imagenet.py)
     * 但是也可以用torchvison.datasets.ImageFolder($DIR, xxx,xxx)
     * 是直接按照默认方式进行读取(读取一个文件夹下的所有东西,直接按照下一级的文件夹名字作为分类标准)
       * trainset.classs_to_idx 自动分配了对应的类别,都是对应的list
   ``` python
   trainset = torchvision.datasets.CIFAR10(root='./data', train=True, download=False, transform=transform_train)
   trainloader = torch.utils.data.DataLoader(trainset, batch_size=128, shuffle=True, num_workers=2)
   ``` 
   * 需要经过一个* [Transform(From Torchvision)](https://pytorch.org/docs/stable/torchvision/transforms.html?highlight=transform)，不仅做了一些对去均值之类的操作，而且是将原本的PIL图像转化为tensor
   ``` python
   transform_train = transforms.Compose([
      transforms.RandomCrop(32, padding=4),
      transforms.RandomHorizontalFlip(),
      transforms.ToTensor(),
      transforms.Normalize((0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010)),
   ])
   ```
   * ```next(iter(trainset{是一个pytorch当中的DataSet}))```返回一个tuple，第一个元素为Data，是一个图。第二个元素为Label，是一个数
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

### 优化器 (来自torch.optim)
```optim.SGD(net.parameters(), lr=args.lr, momentum=0.9, weight_decay=5e-4)```
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


* optimizer.param_groups是一个list
  * 其[0]是一个dict
    * keys - ```dict_keys(['params', 'lr', 'momentum', 'dampening', 'weight_decay', 'nesterov'])```
      * param是一个list，里面是Tensor


### [Tensor](https://pytorch.org/docs/stable/tensors.html)

#### Autograd
    * 声明Tensor的时候默认的require_grad是False哦 ```x = torch.tensor([[1., -1.], [1., 1.]], requires_grad=True)```
        * 用torch.tensor来声明Tensor，torch.Tensor是一个Class
    * 经过计算backward之后，每个tensor的梯度被存在Tensor.grad内部
        * *只有是item单个数目之后的结果才可以做backward哦-很合理这样才是一个计算图*
* torch.apply_(callable) - 对Tensor里的每一个元素执行一样的操作
    * *只能被用在CPU Tensor上而且不能用于高性能需求的时候*

* 可视化(Tensorboard被包含到了torch里面)
    * ```from torch.utils.tensorboard import SummaryWriter```
    * [官方给的Tensorboard的Tutorial](https://pytorch.org/tutorials/intermediate/tensorboard_tutorial.html)


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



# Python
* 我居然又记不住格式控制输出了？！
    * ```print("this is %d" % 20)```
    * ```print("%d---%d"%(10,20))```
    * **Format()** - 用“{}”来代替“%”
        * {}里面带数字可以替换顺序 ```print("{0}>{1}".format(1,2))```
        * {}里面“:x”可以进行进制转换
* enumerate()返回一个枚举对象
    * 输入参数是以一个iterable的对象，比如list
    * 返回的是一个tuple  ```(index, content_of_iter)```
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191014141223.png)
* 迭代器
    * Python内置的iter(list)创建了一个iter对象
    * Next(iter对象)
* 生成器-使用了yield的*函数*（本职需要是一个函数）
    * 是一个返回迭代器的函数，只是运行到yield的时候都保存下所有当前的状态，并返回yield的值
        * *下一次从当前位置开始继续运行！*
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191014142842.png)
        * 由于用到了yield，所以虽然返回了，但是下一次也是在while循环里面的
        


## OOP 
* 一个类的```__init__(self, )```是构造函数
* **类的方法第一个参数要填self！**
* ```self.```相当于this指针
* Python没有New关键字，直接使用类的名称构造对象，相当于调用构造函数```p1=Person(18,0)```
* 内置了一些类的属性
    * ```__dict__``` 包含了一个类的属性所表示的dict
    * ```__name__``` 
    * ```__module__```类定义所在的模块
* 约定俗称的规律```__foo```代表的是private变量
* 而super方法则是通过*调用父类方法来显示调用父类*(可以避免需要用父类的名字来调用父类，需要将self传进去)
    * ```super(BasicBlock, self).__init__()```


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

---

# Matplotlib

* [这里有一个简要上手教程](https://www.jianshu.com/p/c495e663f0ed)\
   
* 默认绘图是Matlab风格(针对列表)
 
``` py
import matplotlib.pyplot as plt
plt.plot([1,2,3,4], [1,4,9,16], 'ro')
plt.axis([0, 6, 0, 20])
plt.show()
``` 

* 或者使用Numpy-based

```py
# evenly sampled time at 200ms intervals
t = np.arange(0., 5., 0.2)

# red dashes, blue squares and green triangles
plt.plot(t, t, 'r--', t, t**2, 'bs', t, t**3, 'g^')
plt.show()
```

* 存图
  * ```plt.savefig("test.jpg")```

# argparse

 ``` python
 parser = argparse.ArgumentParser(description='PyTorch CIFAR10 Training')
 parser.add_argument('--lr', default=0.1, type=float, help='learning rate')
 parser.add_argument('--resume', '-r', action='store_true', help='resume from checkpoint')
 args = parser.parse_args()
 ```


