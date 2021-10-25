---
layout:     post                    # 使用的布局（不需要改）
title:      鸟哥的Linux私房菜           # 标题 
subtitle:   CookBook  #副标题
date:       2024-01-01            # 时间
author:     tianchen                      # 作者
header-img:  img/2021_0603/5.jpg  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 常用
---

# 鸟哥的Linux私房菜的阅读笔记 / 运维笔记

> 学习一下Linux基础 [鸟哥的 Linux 私房菜 -- 基础学习篇目录](http://cn.linux.vbird.org/linux_basic/linux_basic.php)



# Linux-basics

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
    - 可以理解为一个三通管道，截取管道中的内容，输出到某个地方
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



# 一些其他的素材:

1. [Linux工具快速教程](https://linuxtools-rst.readthedocs.io/zh_CN/latest/index.html#)
2. [TLCL](http://billie66.github.io/TLCL/book/index.html)