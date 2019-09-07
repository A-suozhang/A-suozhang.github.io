---
layout:     post                    # 使用的布局（不需要改）
title:      dpu配置的设备树配置与驱动产生              # 标题 
subtitle:   How 2 Connect'em    #副标题
date:       2019-09-07              # 时间
author:     tianchen                      # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 嵌入式
---
# DPU配置
* 本文章内容是用于讲解与整理[在ZCU102上配置DPU](https://a-suozhang.github.io/2019/09/06/ZCU102-Set-Up/)的一部分内容的原理
* 背景知识:
    * 熟悉利用Petalinux编译生成ZCU102的BOOT流程
    * 熟悉Xilinx Vivado SDK PS的使用
    * 熟悉一些基本的嵌入式机理
 
## DPU设备树配置
1. 在完成petalinux的--get-hw=description的conig之后
   * 在petalinux项目目录下的 ./metauser/recipes-bsp/device-tree目录下有 device-tree.bbapend文件(描述了编译设备树所有索引),将其内容改为


```
FILESEXTRAPATHS_prepend := "${THISDIR}/files:"

SRC_URI += "file://system-user.dtsi \
	    file://dpu.dtsi"                        // 这一行是后来加入的,表示要索引当前目录下./files中的dpu.dtsi

```


> 这样的方式本质上是在system-user.dtsi的对应节点下加上了dpu的内容,这样更加清晰

1. dpu.dtsi的位置在刚才目录下的./files/dpu.dtsi中,内容为

```
/ {
        amba{
		dpu{
		    #address-cells = <1>;
    		    #size-cells = <1>;
    		    compatible = "xilinx,dpu";
    		    base-addr = <0x8F000000>;     //CHANGE THIS ACCORDING TO YOUR DESIGN

		    dpucore {
		        compatible = "xilinx,dpucore";
		        interrupt-parent = <&gic>;
		        interrupts = <0x0 106 0x1 0x0 107 0x1>;
		        core-num = <0x2>;
		    };

		    softmax {
		        compatible = "xilinx,smfc";
		        interrupt-parent = <&gic>;
		        interrupts = <0x0 110 0x1>;
		        core-num = <0x1>;
		    };
		};
	};
};
```

![](https://github.com/A-suozhang/MyPicBed/blob/master/img/triangles-1430105_960_720.png)

* 设备树的内容与我们的Vivado Design有着直接关系(本质上就是那个中断号)
* 查询[dpu数据手册]()可知,主要需要修改的是地址和设备号码
    * 其中 base-addr需要与Vivado Design中的Address对应,也就是这里

    ![](https://github.com/A-suozhang/MyPicBed/blob/master/img/20190907113921.png)

    * dpucore的数目与平台有关 (ZCU102是两个)
    * interrupts中的6个数,每3个为一组,两边的0x0和0x1是固定值,内部的106与107则是dpu所对应的中断号
      * ![](https://github.com/A-suozhang/MyPicBed/blob/master//img/20190907114224.png)
      * 如图可见,dpu以及其附属模块给出了一个位宽为7的中断信号接到了PS上
      * ![](https://github.com/A-suozhang/MyPicBed/blob/master/img/20190907114517.png)
      * 这里是那个7位中断信号的内部结构,可以看到,两个dpu核的中断接到了7位信号的[3:2]位,而softmax的中断被接到了最高位
      * ![](https://github.com/A-suozhang/MyPicBed/blob/master/img/20190907115019.png)
      * 在这里配置ZynqPS的两个7位中断,参考[ZU9-PS-INTR数据手册](https://www.xilinx.com/support/documentation/user_guides/ug1085-zynq-ultrascale-trm.pdf)查询
        * ![](https://github.com/A-suozhang/MyPicBed/blob/master/img/20190907120833.png)
            * 感觉和我们实际操作的结果矛盾了...待解决...
      * ![](https://github.com/A-suozhang/MyPicBed/blob/master/img/20190907121356.png)
          * 好像说如果要正常用DNNDK的话只能这么配置
> 曾经尝试过直接改动system-user.dtsi，但是貌似不起作用
>　设备树的改变其实在petalinux-build的一步，如果没有的话改动它没有意义


## DPU驱动
* 参考Petalinux文档[UG1144](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2018_3/ug1144-petalinux-tools-reference-guide.pdf)中的Building UserModule一章节
  * ![](https://github.com/A-suozhang/MyPicBed/blob/master/img/20190907125426.png)
  * 按照这里的步骤走完就可以了
* 之后把生成的dpu.ko拷贝到我们自己的rootfs中,并且需要设置开机自启动
  * 在/etc/rc.local中加入
  ```
  insmod dpu.ko
  ```