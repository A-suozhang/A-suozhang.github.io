---
layout:     post                    # 使用的布局（不需要改）
title:      Shot Something.       # 标题 
subtitle:   拍点东西.    #副标题
date:       2020-04-21          # 时间
author:     tianchen                      # 作者
header-img:  img/4_1/wen.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 杂项
---

> 了解一些背景知识，权且聊以自慰

# 曝光三要素

* 光圈(Aperture) 大小用F值指代，其与光圈半径呈反比(大光圈越难做)
    * 常见配置(1.4/2.0/2.8/4.0/.../F32/F45/F64） 最小光圈到F32
    * 一般大小要用到F15左右
    * F值 = 焦距/光圈直径 
        * 进光面积 正比 光圈半径^2 (F2-f1.4(\sqrt{2})是两倍的进光量)
    * 作用：**控制曝光时通过镜头的单位面积大小**
        * 水龙头比喻： 水龙头的数量(流速)大小
        * 曝光量： 水的体积 = 速度(光圈)*时间(快门时间)
    * *定性Example*
        * F越大，半径小，进光面积小，进光量少，光线越暗
        * 大光圈，焦平面小，景深浅(背景模糊)
        * 大光圈对焦困难，光圈缩小来对焦
* 快门速度:
    * 1/2...1/32...1/8000 - 周期
    * 快的快门速度用在对高速物体的抓拍
    * 物理意义是采样率
        * 一般不能低于帧率，否则就重庆森林
    * 很多人说1/50
    * 光现不充足的时候，可以通过增加快门速度来避免提高ISO影响画质
        * 安全快门(镜头焦段两倍的倒数 50mm镜头 - 1/1^10(2))=1/100s ~ 1/500 s
        * 有防抖的话安全快门可以更慢
    * 较慢的快门速度(进光时间长) - 可以带来动态模糊的效果
* 感光度ISO
    * CMOS感光元件对于进入的光线的敏感程度(增益A)
        * 由于是增益，过大可能会带来噪点的增大
    * 用法Example当快门时间不能再降低(变慢)的时候，为了保证曝光量足够，只有提高感光度
    * 1600-3200可用(50~102400)
* 白平衡 White-Balance
    * 调整颜色倾向和色调
        * 对RAW来说，即使设置错误，也可以修正
    * 自动判别出画面中哪里应该是灰色，并且以此为颜色基准
    * 我们可以自己调整来“欺骗”相机，以获得我们想要的真实颜色(比如拍夕阳 - 白平衡设置为阴影)

---

* 虚化
    * 大光圈
    * 长焦段(镜头性质)： Money is Power
    * 近： 主体接近相机，原理背景
* 拍摄模式
    * AUTO(场景智能模式) - 类似示波器上的Auto，可以救命，但是没用
        * 无法控制对焦和曝光，带上了镣铐
        * 可配置驱动模式(类似单排连拍等功能性模式)
    * P档 - 程序曝光 （Program）
        * 可控制感光度ISO，感光度过高会发白，但是不足可能会欠曝
        * 曝光补偿：白+黑- (白色区域大的时候，会让白色显得发灰，调整补偿 - 比如拍雪)
    * ------ 半自动档 -------(一下两个感光度白平衡等都由我们自己设置)
        * 也需要进行类似的曝光补偿
    * 光圈优先 A档 (Aperture)
        * **适合静止主体**
        * 给出光圈信息，程序计算合适的快门时间
        * 一般不会设置的快门速度不够，但是环境光如果不足的话，ISO低的快门速度低，话容易模糊
    * 快门优先 S/T档  (Shutter/Time)
        * **运动**(环境光变化大)
        * Vice Versa，输入快门时间，计算出需要的光圈大小
        * 可能会出现我们的ISO设置过低导致最大光圈也不能满足足够曝光，需要拉大
    * ------ 手动 ----------
    * 手动曝光 M档 - Manual
        * 需要get的是在多大的光圈下需要设置多大的快门速度
        * 涉及测光 -- 测光表
            * 确定好光圈/快门速度/感光度之后，这个标尺就会根据**对焦位置**给出值 ```f(Focal_length,ISO=,shutter=,aperture=))```
                * 我们调整光圈和感光度，让他归零 ```f=0```
        * 不存在曝光补偿，所以遇到相对的情况，(我们需要拍摄过曝/欠曝时)我们应该控制测光表不到0
            * 因而测光表，代表着**曝光程度**
            * 过曝 - (+)
        * 测光模式  
            * *评价 - 整体参考
            * 局部 - 类似点，参考范围稍大一些
            * *点 - 人像写生，参考范围小，直接对着
            * 中央重点平均 - 整体参考，更倾向中心
            * *中间的面积大，测光参考的画面就多*
* 对焦模式
    * 自动对焦
        * 单次(静止对象)
            * 半按快门可以平移进行二次构图，焦点不会改变
        * 连续
            * 半按快门的时候对焦点会跟踪(一般配合连拍)
    * 对焦点模式
        * 单点自动
        * 区域自动
        * 人脸追踪对焦
    * “跑焦” - 光圈过大

* 驱动模式(最浅显的)
    * 单张/连拍/倒计时(用于防止手指按动快门时候的机械震动)

---

* 构图(偏理念)
    * 网格线拉满)
    * 原则：
        1. 主体人物在竖线，关键点(眼睛)放在交点
        2. 场景分割面，置于1/3水平线
        3. 画面中水平垂直的参考


# 调色
    