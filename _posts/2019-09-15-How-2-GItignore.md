---
layout:     post                    # 使用的布局（不需要改）
title:      如何使用Git            # 标题 
subtitle:   VScode的git插件在非正常使用下陷入混乱        #副标题
date:       2019-09-10              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-dayun0.jpg  #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
     - Coding
---
# 如何正常使用git
> 小金鱼A_suozhang需要一些记录来保证自己不会忘记这些命令

## WorkFlow
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190915105530.png)
* 在git add环节同样也可以git rm (都会对VScode当中的git插件里面状态栏起效果,在staged change当中)
  * ```git add .```把本地的所有文件add进去
  * 本地文件如果只是修改了,没有执行git add,在VScode插件里面只显示CHANGES,如果进行了add之后,则显示STAGED CHANGES
* 在Vscode当中的Commit按钮相当于执行了```git add.```,并且执行了```git commit```
* 在Commit之前,可以用```git status```来查看当前的修改情况
```
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    README.md
        modified:   _posts/2019-09-15-How-2-GItignore.md
```
* 如果本地有没有add的东西,会显示
```
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        deleted:    README.md
        modified:   _posts/2019-09-15-How-2-GItignore.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   _posts/2019-09-15-How-2-GItignore.md

```

## 强行回退版本
1. 首先 ```git log```查看历史Commit,找到自己想要回到的那一次,记录下来COMMIT_ID
2. 执行 ```git reset --hard COMMIT_ID```


## Gitignore
### Tempelate
* 可以使用[Git官方提供的一些模板](https://github.com/github/gitignore),可以排除掉Build所产生的文件

### Place
* 就放在.git文件所在的目录

### Usage
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

## Refs
* [常见Trouble Shooting](https://blog.csdn.net/qq_15437629/article/details/78761459)
* [教你系统学习Git](https://www.jianshu.com/p/fe038a97bb3c)