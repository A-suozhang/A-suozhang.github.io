---
layout:     post                    # 使用的布局（不需要改）
title:      Linux基础               # 标题 
subtitle:   学不会Linux的哲学，学得会Linus的喷人     #副标题
date:       2019-09-06              # 时间
author:     tianchen                      # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 学习笔记
    - Linux
---
# Knowledge About Linux
* 2019/8/20, Suozhang发现自己的嵌入式知识实在是过于贫瘠，记录一下一些关于Linux的知识笔记

## Overview
* 多种发行版
    * Debian
    * Mint
    * Fedora
    * Ubuntu
    * centOS 

## 组成
* linux内核 - image - （由Linus团队维护）
    * 开源(GNU GPL协议)
* Shell 用户与内核交互的接口
    * 包含了   等功能
        * 命令行解释
        * 通配符
            * 和正则中的意思一样
                * “*” 任意数目的字符
                * “？”：任意单一数目字符
                * []: 匹配[]内部任意一个字符
                * [!]：匹配[!之外任意一个字符]
        * 管道
        * Shell语言
        * 命令替换
    * 默认的shell是BASH(Bourne Again Shell)
    * 正则表达式 re
        * grep命令 (Global Search regular Expression)
        ```
        grep [options] PATTERN(可以是字符串也可以是正则) [FILE...(可以是空格分隔的多个文件)]
        ```
* 文件系统
    * linux系统目录树的结构
        * /bin 二进制可执行文件
            * 比如 ls cat mkdir
        * /boot
            * 系统引导时候需要用到的各种文件
        * /dev
            * 设备相关的文件
        * /etc
            * 系统配置文件
        * /home
            * 用户文件
        * /lib
            * 存放根文件系统中程序运行所需要的共享库和内核模块
        * /mnt
            * 系统管理员安装临时文件系统的安装点
        * /opt
            * 额外安装可选的应用程序包所放的位置
        * /proc
            * 虚拟文件系统，存放当前内存的映射
        * /root
            * super user的目录
        * /sbin
            * root才可以访问的二进制执行文件
        * /tmp
            * 临时文件
        * /usr
            * 存放系统应用程序 其中/usr/local是本地管理员软件安装目录
        * /var
            * 存放运行时候需要改变数据的文件（？）
    * Linux下文件有几种类型
        * 一般文件
        * 目录 d
        * 符号链接 l
            * 硬连接：与普通文件类似
            * 软链接：保存了文件的绝对路径（被访问时替换自身路径，类似于Win中的快捷方式）
            * 链接本质上是一种文件共享，来自于POSIX
        
* 第三方软件

## Boot
* 两种启动方式
    * BIOS(Basic Input Output System，也被称为固件)，它会
        * 读取磁盘的前512字节(又被称为MBR(Main Boot record))，前440字节包含了某个**引导程序**
            * 比如说Grub，Syslinux之类的1st Satge Bootloader
        * 然后继续引导或者是直接加载内核
    * UEFI 
        * 能够自动支持文件系统，不收MBR的限制，但是依赖于分区表上的uefi特殊分区
* 内核被加载之后，调用/sbin/init进程
    * init进程为每一个虚拟终端调用getty（初始化tty并且请求输入用户名密码）
    * 输入密码后会与/etc/passwd检查是否正确，正确启动login程序
    * 启动一个会话设置好用户的环境变量，并且启动用户的shell
    * shell启动之后，在显示命令提示符之前加载一个shell的配置文件(比如bashrc)
* 启动流程
    * POST开机硬件自检，完成之后转交给BIOS
    * BIOS检测之后触发中断 INT 13H，指向引导扇区（内含着MBR），准备加载MBR
    * MBR：第一个引导扇区，被加载到内存，控制权给到MBR中的代码
    * Bootloader：（主要有3种引导加载器： GRUB(Grand Unified Bootloader) GRUB2 LILO）
        * Stage1: 加载1.5位置的代码
        * Stage1.5： 开始执行放在文件系统 /boot内的文件驱动程序，并加载相关的驱动程序
        * Stage2：从/boot/grub2/i386-pc

## Device Tree
* .dts
* 主要描述的信息有：
    * cpu的数量和类型
    * 内存的基地址和大小
    * 外设
    * 中断资源
    * GPIO资源
    * CLK资源
* Bootloader将这棵树传递给内核，内核依据其将各种资源(GPIO,IRQ)分配给对应的设备
