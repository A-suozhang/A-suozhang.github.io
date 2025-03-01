---
layout:     post                    # 使用的布局（不需要改）
title:      计算机网络笔记               # 标题 
subtitle:   学校不开课，只能自学啦     #副标题
date:       2019-09-06              # 时间
author:     tianchen                      # 作者
header-img: img/diffusion/cyber-6.png     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 学习笔记
---
# 计算机网络
* 一些关于计算机网络的简单笔记
* 由于自己没有学过相关课程全靠自己理解

## 一些定义
* C/S 模式（Client/Server）   B/S (Browser/Server)
* URL(Uniform Resource Locator) 统一资源定位符 
* 子网掩码： 真实IP是IP AND 上子网掩码
    * 目的是将网络地址和主机地址两部分
    * 255.255.255.0 相当于把后8位去掉，局域网大小小于256个设备

## OSI分层
* 参考OSI的模型可以更好的理解一些概念
* 分层更加分离，更加透明，在每一个层面都可以建立标准
    * 对于这个层次，我们并不需要知道下一个层次是怎么work的，只要接受下一层的数据就可以
        * （HTTP并不需要知道是IPv4还是v6，只需要传过来的数据满足HTTP协议即可）

|||
|--|--|
|物理层|网线|
|数据链路层|网卡|
|网络层|路由器（IP，ICMP）|
|应用层|HTTP、FTP、SMTP、TELNET|
|↕接口|Socket|
|传输层|TCP UDP（DCCP）|

* 路由器对应的协议有比如 IP，ICMP等
    * P是protocol，所以IP协议其实是重复叫法，放在高考语文是病句

## Socket
* Socket被翻译为“套接字”（很台湾风味的一个翻译），是应用层与传输层之间的一个管道
    * 看做是一个应用进程
        * 因为传输层以下一般都是操作系统来控制的
        * 而应用层协议一般通过应用进程来控制
        * 这个接口是一个API，建立应用程序与底层协议的Bridge
    * 面向Socket编程
    * 标识为 IP：port
        * 端口号自动配置或者手动指定
        * 80端口为Web Server（比如Nginx）的默认端口
* 传输层
    * 应用程序之间的逻辑通信
    * 多路复用
        * 和物理层的多路复用（TDM，FDM）不太一样
        * 本质是多个数据报文的同时接受与发送：
            * 对每个数据包都做单独的处理
## UDP & TCP
* UDP
    * 对IP协议的一个很简单的拓展
        * 接受从网络层过来的数据，区分一下PORT号就丢给进程了（可以做差错检测，也可以不做）
        * 简单，高度可定制（在应用层定制）
    * 相对TCP来说不用建立和维护连接，直接传
    * 数据帧中头部占的成分较小
    * 应用层可以控制发送时间和数据（可以完成复杂动作）
    * UDP不提供重传！
* TCP
    * 一个出色的，面向连接的，PORT2PORT的协议
    * 按序字节流协议（好难理解）
    * 接收端和发送端都会存储一定的数据，用于重传和重组
    * 3次握手，4次挥手 
        * 3次握手： （建立连接）
            * 某一端 发出 建立连接的请求
                * 同时会初始化序列号 X
            * 另外一端 同意建立连接请求，并且返回一个报文：
                * SYN=1 ACK=1 
                * 确认报文是X+1
            * 收到确认建立请求报文，再返回一个确认报文
                * ACK=1
                * 确认报文序列号为Y+1
        * WHY Not 两次握手
            * 防止，A发出请求建立连接的一个数据包很久都没有发过去，又发送了一次建立连接请求，这个时候，B收到了之前的那个请求，并且ack了，这时候A收到的B的请求实际上是回应第一次发出的请求，而不是第二次的，这样就混乱了。


        * 4次挥手： （断开连接）
            * A准备断开连接，发送FIN=1，ACK=1
            * B收到了FIN报文，回复一个确认报文（此时仍然可以正常传输数据（可以把之前没有传完的数据接着传））
            * 当B把需要传完的数据都传好了，再给A发送一个FIN报文
            * A收到FIN报文，并且返回一个确认
            * 当B收到A的确认报文之后就可以断开连接了，但是A不行（如果A发出的确认报文在路上丢失了，那么B将会重发FIN报文，这样就会卡死）所以A要在一段时间（一般是2*数据报文的最大存活时间之后）没有收到FIN，也可以断开连接
        * 之所以要4次挥手是因为TCP是全双工的
* 网路层
    * 核心任务： 转发与路由（Route）
    * 需要知道数据去哪儿？
    * 包含了比如IP协议，ICMP协议等
    * 传输层来的TCP/UDP的数据包，需要在网络层添加一个头部，共通组成了IP数据报，然后交给数据链路层
        * 包含了转发的目标（IP）地址
## IP
* IP
    * 长度32bit
    * 划分开成子网
    * 由网络号（位数不确定，大子网位数小）+主机号组成
        * 现在划分子网的方式一般是24（网络号）/8（主机号）
## DHCP
* DHCP (Dynamic Host Configuration Protocol) 动态主机配置协议
    * 动态获取IP就是via这种方式，否则配置静态IP需要子网掩码，DNS服务器地址等
    * 简单而且IP利用率高
    * 具体流程：
        * 发送发现报文 - 想上文的设备在局域网中广播一个DHCP报文，谁会回应就是DHCP服务器
            * DHCP服务器可能不止一个
        * 提供报文 -  DHCP寻找一个available的合适IP
            * 可能有多个DHCP服务器都提供了IP
        * 请求报文 - 想要上文的设备发送请求报文，询问能不能使用某IP
            * 设备在多个IP中随便选了一个，再询问能不能用
        * 确认报文 - DHCP服务器发送确认
            * 所有DHCP看看这个IP是不是自己的，更新自己的表
        * 以上报文包含了IP，子网掩码等信息
    * 一些知识
        * 默认DHCP服务器端口是67
        * 设备的DHCP请求端口默认是68
        * 发现报文 IP：0.0.0.0 目的IP：255.255.255.255
        * 在应用层实现，通过进程实现
## DNS(Domain Name Server)
* 域名解析服务器

## MAC(Media Access Control)
* 唯一标识符
* 在物理层传输中靠它识别身份
* MAC地址是网卡出厂时设定的
    * 由组织和厂家分配
* IP地址是网络层的地址，而MAC地址是数据链路层的地址
* MAC与IP地址之间的映射由ARP(Address Resolution Protocol)协议完成

## 端口
* 全称是协议端口（Protocol Port）可以理解为一个Queue，数据被按照目标端口入队，等待被进程取用
* 一台一个IP地址的设备提供多种服务：比如HTTP，FTP，SMTP等
* 端口号的范围从0~65536（16比特）
* 80 - HTTP； 21 - FTP
* 个人理解应该在网络层，给应用程序分配的

## 网关
* 是一个网络层的概念,PC把所有的IP包都发送到一个地址上,这个地址(用作中转)在路由器中,用于转发 


