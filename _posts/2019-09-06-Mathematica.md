---
layout:     post                    # 使用的布局（不需要改）
title:      Mathematica 学习笔记              # 标题 
subtitle:   总是学不会的优雅的函数式编程     #副标题
date:       2019-12-23             # 时间
author:     tianchen                      # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                             #标签
    - 学习笔记
---

# Mathematica
* 记录下一些常用语法，希望这样使用更加熟练起来...（虽然估计每次用起来都是现场查文档）
* Mathematica的文档很好用
    * guide/
    * tutorial/
* 或者是查询  [网络资料](https://reference.wolfram.com/)

## 总体
* 数据类型
    * 某一个变量的a[[0]]代表了这个变量的类型
    * （由于Mathematica是函数式编程，弱化了数据类型的概念）
* Expression // Function    (斜杠不要打反了...)
    * 等价于Function[Expression]
* % 指代上一个结果
* 强制结束 Alt+. (当运行卡住的时候)
* 清除所有变量
    ```
    Clear["Global`*"]
    ```

## 格式输入
* ```CTRL + /``` 对应/frac
* ```CTRL + ^``` 对应/power

## 表达式
* 可以用FullForm展开表达式的每个部分    
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223090110.png)
* Head可以用来分离表达式的“头部”
  * 有那么一些类似于Type?
  * （还没有完全弄清楚这个应该怎么整）
* 和函数的使用方式区别
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223092638.png)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223093111.png)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223093218.png)
* 对于一个含参数的表达式调整参数绘图
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223095324.png)
  * 分析：
    * 首先对于一个多变量的表达式，首先需要Table来对其赋值```a1 = a```
    * 然后外面先套一层Plot，Manipulate套在最外面，控制这里的参数```a```


## 函数
* 纯函数
    * “#”表示自变量，末尾加上&
    ```
    f = #^2&
    ```
* Function[]定义函数
    ```
    Function[u, 3 + u][x]
    (3 + #) &[x]    // & 指代函数

    Function[{u, v}, u^2 + v^4][x, y]
    (#1^2 + #2^4) &[x, y]
    ```
* 如果需要Manipulate含参数的函数，需要这样
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223094537.png)


## Expr VS. Func

* 赋值方式不同
  * 对于函数直接用 ```f[2]```就可以得到结果，对于expr，可用```Table[expr, {x,{2}}]```来得到类似效果
* Head原理不同
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223104743.png)
* 迭代器的例子
  * Table是对expr赋值的，赋值方式可以理解为**暂时把值置为c，然后计算表达式**（与之对应的需要把参数塞进函数内部）
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223103924.png)
    * 第一种是对表达式赋值，第二种是对函数赋值的方法
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223104149.png)
    

## 绘图
* 基础绘图
    * [guide/DataVisualization]()
    * Plot[{f1,f2},{x,xmin,xmax}]
        * PlotLabels
        * Filling
        * PlotStyle
        * PlotLegends
        * 更多的参考文档中plot目录下的选项
    * ListPlot[{l}]
        * ListLinePlot
    * ArrayPlot
    * MatrixPlot
* 动态绘图
    * [tutorial/IntroductionToManipulate]()
    * Slider (如果只是需要画图的话建议用Manipulate)
    ```
    {Slider[Dynamic[x]], Dynamic[Plot[Sin[10 y x], {y, 0, 2 Pi}]]}
    ```
    * **Manipulate**

    ```Manipulate[Plot[Sin[x (1 + a x)], {x, 0, 6}], {a, 0, 2}]```


    ```Manipulate[Plot[Sin[a x + b], {x, 0, 6}], {a, 2(初值), "Multiplier"}, 1, 4（范围）}, {b, 4, "Phase Parameter"}, 0, 10}]```


     
## List
* 核心数据结构，Mat与Tensor都可以由list嵌套而来
* Apply把list的头部变成新的
* Map将表达式(Expr)对List的每一个元素做
* 可以利用Sequence将list变为seq，便于作为函数的输入

### 构造List
* Range[Min,Max,Step]
    ```
    Range[0., 1., .1]
    ```
* Apply[f,N(num of elements)]
    ```
    Array[2^# &, 4]
    ```
* Table[]
    ```
    Table[2^i, {i, -4, 4}]
    ```
* ConstantArray[a,{i,j,k}]

### List运算
* Length获得长度
* list可以直接作为元素参与运算
    ```
    {3, 5, 1}^2 + 1
    ```
* 也可以直接塞入一些数学函数，比如Sin[],Exp[]
* Part操作支持
* 也支持Append
* Join可以完成concat
    * 直接Join[l1,l2,l3]是在dim1上 （横着concat）
    * Join[l1,l2,2]是在dim2上 （竖着concat）
    * 注意一下定义前面变量的时候不要MatrixForm了，对Dim有影响

## 迭代器
* Table
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223103248.png)
    * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223103629.png)
      * 这里的{0,1}表示0到1，按照整数递增
      * 注意对于expr这样可以直接赋值，如果打印出来是
    * *对于x来说*
      * ```{x,5}```表示对x从0到5
      * ```{x,0,5}``` 表示x开始从0到5
      * ```{x,0,5,0.5}```表示从0开始到5间隔0.5
      * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223105536.png)
* Array(依据function给出一系列)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223100656.png)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223100904.png)
    * {2，2}表示生成[2,2]这么大的内容
    * {0，1}表示横纵轴分别从0，1开始
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191223102408.png)

* Map
    * 将 f 应用到 expr 中第一层的每个元素.
    ```
    Map[f,{a,b,c,d}]
    f/@{a,b,c,d}
    ```
* Apply 

    ```
    Apply[f,{a,b,c,d}]
    f@@{a,b,c,d}
    ```

    * Map与Apply的区别
  
    ```f /@ { 1,2  , 2, 3}```
    ```>>> {  1, 8  , 8, 27} // 更符合常规认知```
    ```f @@ {  1, 2, 3, 4  ,   2, 3, 3  , 3}```
    ```>>> {1, 8, 27, 64}  //其实是将{1,2,3,4}作为函数的第一个参数输入了，{2,3,3}被作为第二个参数```



## 获取数据
* Directory[] 获取当前目录  
    * SetDirectory[] 设置目录 （目录要加引号，正反斜杠都可以）
* Import[file_dir,"Data"] 这样返回的是一个List
    * 注意Mathematica的索引从1开始，索引位置0代表的是数据类型
* Interpreter （具体使用方式有一些复杂）

## 数学
* Log10/Log2[]
* Simplify 化简式子
* Solve 解方程

## 数据处理
### 进制转换 
* 参考了Mathematica文档 [tutorial/DigitsInNumbers]()
    * BaseForm: 以N进制展示x
        ``` bash 
        BaseFrom[x,N]  
        ```
        * 可以小数直接转化嗷(不过这里输出的数据是BaseForm形式的，不能简单的做加减运算)
        ```
        BaseForm[1.125,2]
        >>> 1.001
        ```
    * IntegerString
        * 给出进制转换之后的字符串 String
        ```
        IntegerString[256,2]
        ```
    * IntegerDigits
        * 给出进制转换之后的各位数字的list
            * 可以输入第三个参数（长度），不够的时候左边添0
        ```
        IntegerDigits[256,2]
        >>> {1, 0, 0, 0, 0, 0, 0, 0, 0}
        ```
    * RealDigits
        * IntegerDigits第一个参数只能是int
        * [1.25,N,L] - 待转换的数，进制，长度
    * FromDigits: 从二进制序列中恢复原先的数
        ```
        FromDigits[{1, 1, 1, 1, 0, 1}, 2]
        >>> 61
        ```
    * N进制数据的表示 N^^1234
        ```
        16^^FE
        >>> 254
        ```
### Matrix Manipulation
* Dimensions[m] 查看    
* Part 索引
    * [m,行数,列数]
* [[]]简化的Part
    * a[[1;;3]]
    * a[[1;;]]
* ArrayReshape[m,{i,j,k}]
* PadRight/Left/Array  填充不规则数组
* MatrixForm看上去会比较舒服，但是赋值的时候会把矩阵维度变为{1}


## 一些奇特功能
* TCP SOCKET
``` bash
s = SocketConnect["169.254.190.51:100"]
Read[s]
WriteString[s, "1234"]
Close[s]
```
