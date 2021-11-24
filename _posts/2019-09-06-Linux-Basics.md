---
layout:     post                    # 使用的布局（不需要改）
title:      Linux基础补课               # 标题 
subtitle:   学不会Linux的哲学，学得会Linus的喷人     #副标题
date:       2019-09-06              # 时间
author:     tianchen                      # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 学习笔记
    - Linux
---

# 鸟哥的Linux私房菜的阅读笔记 / 运维笔记

> 学习一下Linux基础 [鸟哥的 Linux 私房菜 -- 基础学习篇目录](http://cn.linux.vbird.org/linux_basic/linux_basic.php)

# 网络相关

> 需要恶补一下网络知识了呢！

# Frac-knowledges

1. 所谓“命令行”本质上指的是shell，它是一个程序，接受键盘的输入，将命令传送给OS来执行。
    - 几乎所有的linux发行版都提供了一个bash(它是GNU项目中的shell程序) - "Bourne Again SHell",bournes是一个人名，他写了Shell(SH)的improved version.
    - 只有在使用用户界面的时候，我们使用的Terminal，它是一个“终端仿真器(Emulator)”，让图形界面和shell交互
    - shell前面的提示符，一般是“$”,如果是“#”,那么表示是root
    - 幕后的控制台: 即使没有terminal正在运行，后台仍然会运行着几个会话，大多数Linux系统中都可以用`CTRL+ALT+F1~6`
2. 使用less来看文件内容（优雅），上下滚，q退出
3. 通配  `*  ？  []    [!]` 
4. 硬连接的问题是：不能跨磁盘或者是连接一个目录
5. man某个命令来看args
    - whatis+某命令来看间接用法
6. 重定向 ` > xxx.log`
    - ` xx 2> xx` 错误流
    - `2>&1` 重定向标准错误流(文件描述符2)到(文件描述符1)
    - 便捷方法 ` ls -l /bin/usr &> ls-output.txt` 直接重定向标准输出和错误到文件
    - 丢掉不需要的输出 `2> /dev/null`
7. cat读取文件并读取到标准输出
    - 利用cat写入文件: `cat >$FILE` 键盘输入的内容就会写入，用`CTRL+D`来退出
8. tee从标准输入流读，送到标准输出流
    - 可以理解为一个**三通管道**，截取管道中的内容，输出到某个地方
    - `ls /usr/bin | tee ls.txt | grep zip`
9. Move Cursor:
    - `CTRL+F/B` 前后一个字符
    - `ALT+F/B` 前后一个单词
    - `CTRL+ k/u` 删除到最后。前
    - `ALT+D` 删除当前的单词
10. `CTRL+r` 增量搜索
    - 输入索引值，继续输入`CTRL+r`以切换，回车以键入命令行
11. 进程(process）由内核管理，内核在启动时候将一些活动初始化为init进程(PID总为1)
    - daemon(守护进程)通常在后台运行
    - `ps aux`
    - ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210921195020.png)
    - 检测进程相关的 `pstree vmstat xload tload` （后两个依赖于图形界面）
12. `printenv` 看所有环境变量
13. bash启动时候可能读取的文件
    - `~/.profile  /etc/profile`
    - `~/.bash_profile   bash_login`
    - `/etc/bash.bashrc ~/.bashrc`
14. 包管理工具:
    - debian-like: 低层dpkg，上层apt-get
    - Fedora/RedHat/CentOs, 低层rpm，高层yum
15. 磁盘
    - 虚拟存储设备： RAID(Redundant Array of Independent Disks) & LVM(Logical Volume Manager)
    - 命令 
        -  fsck： check and repair file system
        - fdisk: 分区表控制器
        - mkfs: 创建文件系统
        - dd: 直接写入磁盘
    - `/etc/fstab`  
        - ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20210922092004.png)



# IPADS实验室的shell教学

>  [IPADS新人培训第一讲：Shell-哔哩哔哩](https://b23.tv/8DIwrX)， 上交IPADS组的一个shell教程，非常推荐看一下，包含了一些command-line tools以及bash-scripts的使用

1. Shell以及CLI的基本概念辨析
    - CLI描述的是基于命令行的交互方式，对应GUI
    - shell是一个系统级的CLI程序
    - Bash是"bourne again shell"，一种更加advaned shell
    - terminal(emulator) 是一个”终端仿真器“，bridge了你所看到的图形界面和CLI
2. pipe与redicrect, 组合command line工具
    - 管道 `|`
    - 重定向: `> < &`
        -  一般2是默认错误流 `./test > test-out.log 2>test-error.log`
3. 简单function
    - sort
    - wc(word count)
    - ag(find keyword), 直接search当前目录下的特定关键词可能出现的位置 `sudo apt-get install silversearcher-ag`
        - also支持regex以及stdin  `ag "^something$" `   `ps -a | ag nvidia`
    - awk(text-processing language)
        - `cat tmp | awk '{print $2}'` - 打印第二列， awk默认每一行都触发一次，所以每行都打印出第二个数据(默认空格符作为分割)
        - awk支持 begin和end，可以在文件的开始和结束做操作，可以比如说做AVERAGE `cat tmp | awk 'BEGIN {cnt=0} {sum+=$1; cnt+=1} END {print (sum/cnr)}'` 
        - conditional logic, `cat tmp | awk '{if($1>3) print $2}'`
    - sed: 
4. bash script



# 一些其他的素材:

1. [Linux工具快速教程](https://linuxtools-rst.readthedocs.io/zh_CN/latest/index.html#)
2. [TLCL](http://billie66.github.io/TLCL/book/index.html)



---

> 下面是Deprecated的一些知识，在2019-09-06被更新，两年多之后我发现我仍然没有掌握各种基础，所以过来renew一下

# Knowledge About Linux

> 2019/8/20, Suozhang发现自己的嵌入式知识实在是过于贫瘠，记录一下一些关于Linux的知识笔记

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
