---
layout:     post                    # 使用的布局（不需要改）
title:      Understanding&Debugging PyTorch           # 标题 
subtitle:   Also A Bit About Python        #副标题
date:       2019-10-14              # 时间
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


### 读取数据集
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

### 优化器 (来自torch.optim)
    ```optim.SGD(net.parameters(), lr=args.lr, momentum=0.9, weight_decay=5e-4)```
    * 所有的optimizer都会实现一个step()方法，利用它来*实现参数的更新*（前提是之前执行过计算图的```loss.backward()```）
    * loss的两个输入参数形式是（可能会有long tensor的要求）
    ``` py
    input = torch.Tensor([[0.1,0.8,0.1],[0.4,0.6,0]])
     # 3 Class, 2 Batch_size
    target = torch.Tensor([1,1])
    ```

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
* 有一个很好的train的example，已经fork到workspace当中了

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


---
* argparse库
   ``` python
   parser = argparse.ArgumentParser(description='PyTorch CIFAR10 Training')
   parser.add_argument('--lr', default=0.1, type=float, help='learning rate')
   parser.add_argument('--resume', '-r', action='store_true', help='resume from checkpoint')
   args = parser.parse_args()
   ```


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


# TroubleShooting

* matplotlib

