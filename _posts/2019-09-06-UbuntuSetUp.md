---
layout:     post                    # 使用的布局（不需要改）
title:      Ubuntu 配置心得               # 标题 
subtitle:   已经不知道是第几次安装Ubuntu了 #副标题
date:       2019-09-06              # 时间
author:     tianchen                      # 作者
header-img:     #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 环境配置
    - Linux
---
# Set Up Ubuntu

## 选择版本
* 尽量选择LTS的版本（16.04/18.04）
* 不要选择太老的版本
  
## Win下的准备
* 官网下载镜像（xx.img）
* 下载Universal USB Installer制作启动盘
* 磁盘管理中把盘分好
  * 注意有的时候合并分区不能跨分区
  * 注意在安装了ubuntu之后在windows分盘的时候不要把带grub的分区清空
* 
## 安装
* 开机时候进入BIOS（thinkpad貌似是F12？）
* 关闭Secure Boot并且把U盘的启动设置高优先级
* 选择英文可能没有中文输入法
* 再度开机时候就进入安装界面了
  * 关闭在安装时同时安装软件等东西
  * **一定不要选择清除已有的系统安装**
  * 建议直接按照默认让他帮你配置boot等分区
* 
## 开机配置

### 切换软件源
* 先切换源否则apt-get会很慢
* 在software & Update中，选择更换源
  
### 安装中文输入法
* 我装的时搜狗，貌似还需要配置fctix（Ubuntu的输入法管理）
  * 参考[这个链接](https://www.cnblogs.com/BlackStorm/p/10359254.html)
  
### 安装必要软件
* 以下基本都可以通过直接在官网下载.deb安装
  * chrome
    * chrome登录之后如果没有同步收藏夹，需要修改host，参考[这个链接](https://blog.csdn.net/nyist327/article/details/22929553)
    * 这一步如果不做的话可以后面如果shadowsocks配置好了理论上也可以（？实际上没有尝试过）
  * 网易云
  * vscode
    * Sudo 使用VSCODE的时候在末尾加上--user-data-dir
    * 乱七八糟插件装起来
      * Power Mode： 闪光插件，很好玩
      * Markdown
      * Python等语言的支持
  * 坚果云

### Bash相关
* [环境变量的配置](https://blog.csdn.net/nyist327/article/details/22929553)
* 可以通过这种方式在写指令的同时输入密码
  ```
  echo 123456|sudo -S ls
  ```
* 或者通过这种方式写log
  ```
  echo "看到这行字，说明添加自启动脚本成功。" > /usr/local/test.log
  ```

### 科学上网
1. Shadowsocks客户端配置
* 首先你需要已经有一个Shadowsocks服务器，并且获得
  * 服务器地址
  * 密码 & 加密方式
  * 本地端口（后面会一直用到）
* [教程1](https://mystery0.vip/2017/01/12/Ubuntu%E4%BD%BF%E7%94%A8Shadowsocks-qt5%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91/)
* [教程2](https://note.janhui.xyz/blog/post/janhui/ubuntu%E5%AE%89%E8%A3%85shadowsocks)
* *我个人没有采用带GUI的安装，因为它有点丑而且老*
* 推荐采用教程二的pip安装方式
* 安装完成之后在根目录编辑 shadowsocks.json,然后调用
```bash
sslocal -c PATH_TO_YOUR_SHADOWSOCKS_JSON (~/shadowsocks.json)
```
2. Chrome配置
* 在[Chrome应用商店](https://chrome.google.com/webstore/)中安装Switchy Omega
  * （如果你不科学上网下不了这个插件...而你想要科学上网又需要这个插件，所以要么拷贝或者找国内分享吧）
  * 在Switchy Omega中首先建立一个情景模式
    * “Proxy” - 地址为本地地址 127.0.0.1 端口为你在shadowsocks.json里面配置的**本地端口**
    * “Auto Switch”模式也可以勾选了（这里可以导入PAC文件配置来选择哪些网站需要走ss那些不要）
3. 开机自启
  * 参考[这个链接](https://blog.csdn.net/qq_25241325/article/details/80691271)
  * 可以选择开机自启动，也可以在指令后面加一个“&”（不然打开这行指令之后这个Terminal就不能干别的了，当然如果你关掉这个Term SS也失效了）
  ```bash
  sslocal -c PATH_TO_YOUR_SHADOWSOCKS_JSON (~/shadowsocks.json)
  ```
* 建立开机启动脚本的时候可以通过这种方式在写指令的同时输入密码
  ```
  echo 123456|sudo -S ls
  ```
* 或者通过这种方式写log
    ```
    echo "看到这行字，说明添加自启动脚本成功。" > /usr/local/test.log
    ```
* 和上面链接不同的是Ubuntu 18.04不支持使用rc.local了，改用[这个](https://www.cnblogs.com/airdot/p/9688530.html)
  * [这个](做了一些原理上面的解释)
  * 卡在了 systemctl start service 上，很迷，不知道是不是因为sslocal的原因，目前原因未知
  * 最后的解决方法是没用用到那个sh，直接在建立出来的那个rc.local里面加上了sslocal的指令并且加上了"&"效果达成，成功利用proxy模式链接上了某P站
4. 添加Proxychains以在Term中使用梯子
  * 先
  ```
  sudo apt-get install proxychains
  ```
  （貌似apt的archive里有proxychains和proxychains4，我用的是前者）
  * 编辑/etc/proxychains.conf
  * 注意设置梯子的模式（我的是SOCK5，设置SOCK4的时候会报错）
  * 将端口号设置为我们之前的本地端口
  * 之后在命令行命令前面加上proxychains就可以了
  ```
  proxychains git clone https://xxx.git
  ```
5. 配置下载
* [通过Aria2c+chrome百度云下载](https://zhuanlan.zhihu.com/p/55212887)

#### V2Ray
* 参考了[V2Ray白话文指南](https://guide.v2fly.org/prep/)
    * 以及其他的几个链接  
        * [link1](https://huifeng.me/2018/10/12/The-Right-Way-Make-Linux-Desktop-Use-V2ray-Service/)
        * [link2](https://www.imcaviare.com/2018-12-18-1/ )
* 注意V2Ray服务器会按照时间进行对照，所以需要做时间校准(虽然我的Ubuntu安装正常)
1. 下载脚本 ```wget https://install.direct/go.sh```
2. 执行安装脚本 ```sudo bash go.sh```
    * 如果直接这样下载的话他会默认选择从git下载，但是很多时候git也是被墙的，所以是一个左脚踩右脚上天，很蠢
    * 首先下载 v2ray-linux-64，可以在脚本指定本地文件(复制到指定目录下) ```sudo bash go.sh --local ./v2ray-linux-64```
        * 下载我是通过能翻墙的机子从[这里](https://github.com/233boy/v2ray/wiki)下载的
        * 分享一个[百度云](https://pan.baidu.com/s/18SlswO73uoJf0zDjTKyUdQ)把,提取码cuvy
    * 运行完成之后应该就安装成功了
3. 安装成功之后不会立即启动V2ray服务,手动开启  ```systemctl start v2ray```
    * 关闭用```systemctl stop v2ray```
    * 开机启动```systemctl enable v2ray```
    * 这之后采用比如 ```pstree``` 等命令查看正在运行的服务，可以看到v2ray正在运行
4. 添加配置文件
    * 配置文件在 /etc/v2ray/config.json
    * 可以自己写，也可以利用Win或者其他平台下用GUI配置好的config.json文件直接复制过来替换
5. Chrome浏览器配置
    * 同样可以使用Switchy Omega,proxy中选择SOCK5,地址127.0.0.1，端口选择config.json文件中的端口(in my case, 10808)
    * Proxy模式开启V2Ray就能够正常运行了
6. proxychains的配置也要做对应修改，修改本地端口




## 一些Win下的应用
* 安装deepin wine以安装一些Win下软件
* [Deepin-Wine的安装与配置](https://github.com/wszqkzqk/deepin-wine-ubuntu)
* 在官网上下载安装包（.deb）双击直接安装
  * 或者
  ```
  sudo dpkg -i xxx.deb
  ```
* 直接通过应用程序即可打开
* [deepin' wine 的应用商店](https://dstore-appstore.deepin.cn/china/index)
* 推荐的比如
  * TIM
  * 微信
  * [OneNote](https://github.com/patrikx3/onenote)

## 我最喜欢的美化时间！
* 仿Mac风格
  * [教程1](https://blog.csdn.net/qq_28869927/article/details/80917997)
  * [教程2](https://zhuanlan.zhihu.com/p/71588449)
