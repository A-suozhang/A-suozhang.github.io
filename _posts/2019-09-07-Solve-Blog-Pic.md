---
layout:     post                    # 使用的布局（不需要改）
title:      解决博客中图片不显示的问题              # 标题 
subtitle:   不是我寻思我发的图片都很健康啊    #副标题
date:       2019-09-07              # 时间
author:     tianchen                      # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 环境配置
    - debug
---
# 问题描述
* 采用JekyII搭建博客之后,花了一个上午鼓捣了一个白嫖图云(充分利用github),然后发现图云到博客里面没有效果
* 在本地用VScode的Markdown渲染,没东西
* 在github别的repo里面插入链接,能用


# 问题解释
* ```https://github.com/A-suozhang/MyPicBed/blob/master/img/andromeda-galaxy-755442__340.jpg```这个是从git中直接浏览图片的链接
* 长这样
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190907135124.png)
* github里面markdown的解析方式貌似 能够把这种图片解析出来,但是博客不行
* *把上面的blob改成raw之后,会出现这个界面,这个才是我们所需要的*
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190907135232.png)

## etc
* 设置着突然token没了?? *问题所在是一个Token只能有有一台机器,否则会被清空*
* Ubuntu d5cf21946ceeba31353675301ba01af475b0a804
* Win 58eee05a216b1a93d525d85a55d9f46fe3a0c7a2
