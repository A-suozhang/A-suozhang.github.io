---
layout:     post                    # 使用的布局（不需要改）
title:      如何使用Gitignore              # 标题 
subtitle:   不然git-repo一会就塞满了        #副标题
date:       2019-09-10              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-dayun0.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
     - Coding
---

# 如何撰写一个Gitignore

## Tempelate
* 可以使用[Git官方提供的一些模板](https://github.com/github/gitignore),可以排除掉Build所产生的文件

## Place
* 就放在.git文件所在的目录

## Usage
* 保存.gitginore之后可以使用```git status```来检查是否正确(Vscode直接用git框子里面看)
* 也可以用```git check-ignore -v XXX```
* 主要用到的 (支持逻辑通配)
  *   *.pth
  *   folder1/folder2   
*  已经上传的,后修改了gitignore (清空缓存可以解决)
```
git rm -r --cached .
add .
```