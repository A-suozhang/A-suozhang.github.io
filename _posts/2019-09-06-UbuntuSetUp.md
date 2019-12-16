---
layout:     post                    # 使用的布局（不需要改）
title:      Ubuntu 配置心得               # 标题 
subtitle:   已经不知道是第几次安装Ubuntu了 #副标题
date:       2019-12-14            # 时间
author:     tianchen                      # 作者
header-img:  img/11_30/bg-desk0.jpg #这篇文章标题背景图片
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
  * 或者直接搜索*搜狗输入法For Linux*下载deb安装，重启之后
    * 在fcitx当中添加（首先取消勾选Only Show Current Language英文系统的化应该找不到），在最后应该能找到sougou pinyin
    * 勾选并置顶
  * SHift+Space是全角半角切换   
  
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
  * ```sudo pip install shadowsocks```
* *我个人没有采用带GUI的安装，因为它有点丑而且老*
* ~~推荐采用教程二的pip安装方式~~ 后来又试了一次教程一，也不错

* 安装完成之后在根目录编辑 shadowsocks.json,然后调用
``` bash
sslocal -c PATH_TO_YOUR_SHADOWSOCKS_JSON (~/shadowsocks.json)
```
* 可能会出现问题 ```AttributeError: /usr/local/ssl/lib/libcrypto.so.1.1: undefined symbol: EVP_CIPHER_CTX_cleanup```
  * 参考[这个博客的解决方案](https://kionf.com/2016/12/15/errornote-ss/)
  * 我们实际的路径可能是 ```./.local/lib/python3.6/site-packages/shadowsocks/crypto```

1. Chrome配置
* 在[Chrome应用商店](https://chrome.google.com/webstore/)中安装Switchy Omega
  * （如果你不科学上网下不了这个插件...而你想要科学上网又需要这个插件，所以要么拷贝或者找国内分享吧）
  * 在Switchy Omega中首先建立一个情景模式
    * “Proxy” - 地址为本地地址 127.0.0.1 端口为你在shadowsocks.json里面配置的**本地端口**
    * “Auto Switch”模式也可以勾选了（这里可以导入PAC文件配置来选择哪些网站需要走ss那些不要）
* 其他一些推荐的Extension
    * Stylus - 改变网站的样式
    * dark Chrome - 暗色模式
    * Momentum 欢迎页面
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

## 多工作区
* CTRL+ALT+上下方向箭
  * 可以有效提高效率

## 内存相关

> 参考了[This Blog](https://www.xilinx.com/products/design-tools/ai-inference/ai-developer-hub.html#edge)

> 以及[This](https://www.howtoforge.com/tutorial/linux-swappiness/)

> 还有[CSDN的这个](https://blog.csdn.net/xingyu15/article/details/5570225)

* 创建交换空间
  * 查看目前的空间 ```free -m```,或者进```system-monitor```看图形界面,或者用```grep SwapTotal /proc/meminfo```
  * 首先取消目前的交换空间```sudo swapoff -a```
  * 创立swap文件  ```sudo dd if=/dev/zero of=/swap bs=1G count=8```
    * dd是分配磁盘空间的函数,if表示输入文件,of表示输出文件地址
      * 其中```/dev/zero```是linux系统中的特殊文件[是一个伪文件](https://www.cnblogs.com/lishihai/p/7986565.html)
    * 一个block 1G,一共8个
  * 让文件被用于swap```sudo mkswap /swap```
  * 激活 ```sudo swapon /swap```
    * (中间可能会报一个关于权限的错误,但是无所谓)
  * 再次查看应该就对了

* 修改**Swappiness**
  * 表示了使用swap空间的倾向,Ubuntu默认是60(如果为0的话物理空间用完之后才会使用swap空间)
  * 查看``` cat /proc/sys/vm/swappiness ```,系统`默认是60
  * 临时修改```sudo sysctl vm.swappiness=10```
    * 这样修改之后下次开机还会继续改为60,所以,修改```sudo vim /etc/sysctl.conf```加上```vm.swappiness=10```

  
## Trouble Shooting
* 硬盘识别不出来```Mount error: “unknown filesystem type 'exfat'”```
  * ```sudo apt-get install exfat-fuse exfat-utils```


## Vim Set Up

> 参考了实验室上一任网管的神奇Vim配置(我是不是没有转载的权限，所以我给自己存着看...修改了一定的不适应的部分)

1. 安装Vim

``` 
sudo apt-get install vim git
mkdir -p ~/.vim/bundle 
git clone https://github.com/gmarik/Vundle.vim.git ~/.vim/bundle/Vundle.vim  # vim 插件管理器
```

2. 安装一些其他项

```
sudo apt-get install silversearcher-ag ctags
```

3. 源码编译安装Global

```
sudo apt-get install libncurses5-dev # 插件需要的软件包
wget http://mirrors.ustc.edu.cn/gnu/global/global-6.4.tar.gz
tar xf global-6.4.tar.gz
cd global-6.4
./configure && make && sudo make install
```

4. 建立.vimrc (这个博客里格式显示好像有一点小问题)
   * 这个脚本貌似不联网的话有点慢，但是总归能够跑完 

``` bash

set nocompatible   " 必须, 关闭 vi 兼容模式
filetype off       " 必须
 
" 设置 Runtime Path，供 Vundle 初始化
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
 
" 让 Vundle 管理 Vundle，必须
Plugin 'gmarik/Vundle.vim'
Plugin 'rking/ag.vim'
Plugin 'kien/ctrlp.vim'
Plugin 'Yggdroot/indentLine'
Plugin 'scrooloose/syntastic'
Plugin 'scrooloose/nerdtree'
Plugin 'majutsushi/tagbar'
Plugin 'bling/vim-airline'
Plugin 'jrosiek/vim-mark'
Plugin 'bigeagle/molokai'
Plugin 'aceofall/gtags.vim'
Plugin 'hdima/python-syntax'
Plugin 'hynek/vim-python-pep8-indent'
 
call vundle#end()
 
colorscheme molokai " 使用 molokai 主题，请注意需要 256 色终端
set so=10     " 光标到倒数第10行开始滚屏   
syntax on     " 语法高亮
set number    " 显示行号
set autochdir " 打开文件时，自动 cd 到文件所在目录
 
" 文件类型支持
filetype on
filetype plugin on
filetype indent on
 
set list lcs=tab:\¦\   " 使用 ¦ 来显示 tab 缩进
 
" 缩进相关
set shiftwidth=4
set tabstop=4
set softtabstop=4
set smartindent
 
if has("autocmd")  " 打开时光标放在上次退出时的位置
    autocmd BufReadPost *
        \ if line("'\"") > 0 && line ("'\"") <= line("$") |
        \   exe "normal g'\"" |
        \ endif
endif
 
set completeopt=longest,menu " 自动补全菜单
 
" 鼠标支持
if has('mouse')
    set mouse=a
    set selectmode=mouse,key
    set nomousehide
endif
 
set modeline      " 底部的模式行
set cursorline    " 高亮光标所在行
" set cursorcolumn  " 高亮光标所在列
 
set showmatch     " 高亮括号匹配
set matchtime=0
set nobackup      " 关闭自动备份
 
set backspace=indent,eol,start
 
 
" 文件编码
set fenc=utf-8
set fencs=utf-8,gbk,gb18030,gb2312,cp936,usc-bom,euc-jp
set enc=utf-8
 
" 语法折叠
set foldmethod=syntax
set foldcolumn=0  " 设置折叠区域的宽度
set foldlevel=100
" 用空格键来开关折叠
nnoremap <space> @=((foldclosed(line('.')) < 0) ? 'zc' : 'zo')<CR>
 
 
set smartcase
set ignorecase  " 搜索时，智能大小写
set nohlsearch  " 关闭搜索高亮
set incsearch   " 实时显示搜索结果
 
 
" 让 j, k 可以在 自动wrap的行中上下移动
vmap j gj
vmap k gk
nmap j gj
nmap k gk
 
" Shift-T 开新 Tab
nmap T :tabnew<cr>
 
" 以下文件类型，敲 {<回车> 后，自动加入反括号 }
au FileType c,cpp,h,java,css,js,nginx,scala,go inoremap  <buffer>  {<CR> {<CR>}<Esc>O
 
" ------ python  ------------
let python_highlight_all = 1
au FileType python setlocal ts=4 sts=4 sw=4 smarttab expandtab
" ------ python end ---------
 
" ------- Tagbar ------------------
let g:tagbar_width = 30
nmap tb :TagbarToggle<cr>	" 使用 tb 开/关 Tagbar
 
" ------- Tagbar End --------------
 
" ------- Gtags -------------------
set cscopetag				 " 使用 cscope 作为 tags 命令
set cscopeprg='gtags-cscope' " 使用 gtags-cscope 代替 cscope
let GtagsCscope_Auto_Load = 1
let GtagsCscope_Quiet = 1
 
" ------- Gtags End ---------------
 
" ------- NerdTree -------------------
 
nmap nt :NERDTreeToggle<cr>
let NERDTreeShowBookmarks=0
let NERDTreeMouseMode=2
let g:nerdtree_tabs_focus_on_files=1
let g:nerdtree_tabs_open_on_gui_startup=0
 
let NERDTreeWinSize=25
let NERDTreeIgnore = ['\.pyc$']
let NERDTreeMinimalUI=0
let NERDTreeDirArrows=1
 
"let g:newrw_ftp_cmd = 'lftp'
let g:netrw_altv          = 1
let g:netrw_fastbrowse    = 2
let g:netrw_keepdir       = 1
let g:netrw_liststyle     = 3
let g:netrw_retmap        = 1
let g:netrw_silent        = 1
let g:netrw_special_syntax= 1
let g:netrw_browse_split = 3
let g:netrw_banner = 0
" ------- NerdTree End ---------------
 
" ---- Airline 
"set laststatus=2
"let g:airline#extensions#tabline#enabled = 0
"let g:airline_powerline_fonts = 0
"let g:airline_theme = "powerlineish"
" ---- Airline
 
 
" vim: ft=vim

```


5. 这个打开之后应该会有报错，不管他，进入vim，执行```：PluginInstall```
   * 还有修改一个地方否则配色会报错(我不知道为啥)

6. 
* ~/.vim/bundle/molokai/colors/molokai.vim:line  132:
  * none -> NONE

7. 效果
   * * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191021090009.png) 

8. 额外安装一些包来支持剪贴板
   * ```sudo apt-get install vim-gtk vim-scripts vim-gnome```
   * 用````vim --version | grep clipboard```来检查是不是支持
   * 用```+"y```来剪切vim中的内容到系统剪贴板 
   



## 一些其他软件
* Shutter
  * ```sudo apt-get install shutter```
  * 设置快捷键
    * 在Settings->Keyboard->添加一个```shutter -s```，配合你想要的快捷键
* PicGo
    * 参考[建立博客的博客](http://a-suozhang.xyz/2019/09/09/Set-Up-Blog/)
* Filezilla 
  * [this link](https://www.atechtown.com/install-filezilla-on-ubuntu/)
  * 就是正常的apt配置
  * **对于这些应用程序，不想开一个Term来打开的方法**
    * 直接win，打开
    * alt+f2输入
* PDF阅读软件 [Mendeley](https://www.mendeley.com/?interaction_required=true)
    * 我貌似没有用apt安装成功，所以是直接用wget下的
* 发现Vscode窗口很多个认不出来
  * 安装[Peacock插件](https://papapeacockstorage.z13.web.core.windows.net/)每个窗口整一个颜色
  * ~~曾经我认为这个插件很脑瘫,没想到真的用到了~~
* 设置默认程序的打开方式
  * 一个文件右键```"properties->open with->set as default"```


# Win当中的Ubuntu - WSL(Windows SubSystem Linux)
* 按[这篇文章进行了一些配置](https://zhuanlan.zhihu.com/p/57556340)
    * 这篇文章里修改登录地点（建立/etc/wsl.conf之后我找不到Win的文件了，好迷...）删除文件并且重启之后恢复来
    * Win的文件在 **/mnt/c/User/xxx** 下
* 在配置ssh的时候出来一些很灵性的问题
    * 最后在我的Moba Xterm里直接出现了一个Ubuntu WSL可以连，很奇特嗷
    * 然后cmder里面用vim粘贴多行貌似有很奇特的格式bug（指回车被解析为“^M”） 
* 和之前一样配置了vim
    * 在编辑.vimrc的时候遇到了cmder的粘贴问题
    * 用MobaXterm解决了
* 配置ssh到跳板机器
    * 我选择了把密钥复制过来(这个操作其实不是很好嗷)然后遇到了权限太高的问题
    * 把id_rsa的权限改成600解决了


# 关于服务器上Ubuntu的配置[EVA 开发日记](http://a-suozhang.xyz/1024/10/20/Eva/)

## Jupyter Notebook 
* 安装没有问题了
* 启动的时候，初次登陆需要验证token，就是term中的这种
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191209123716.png)
* 安装jupyterthemes，直接用pip安装即可
  * 然后导入配置 ```jt -t onedork -f roboto -fs 14 -nfs 14 -tfs 14 -ofs 11```
  * (如果用apt安装jupyter可能会出现无法往.jupyter目录写的问题，手动修改权限即可)

## Tmux
* Tmux-Config
* 解决tmux下的vim配色错误）
  * 用```echo $TERM```如果是screen那就不对，需要改为xterm
  * ```set -g default-terminal "screen-256color"```
* 进入tmux，CTRL+B然后冒号输入指令
  * ```source-file ~/.tmx.conf```

## SSH 配置

* Secure Shell，其产生初衷是因为FTP/TelNet等传明文上不安全
* 安全验证方式
  * 基于密码的登录
    * 远程主机将自己的公钥分发给需要登录的客户端,客户端访问主机的操作利用该公钥加密,远程主机则使用自己的私钥来解解密数据
    * 登录时,本机给远程主机发请求,远程主机把公钥给本机(这一步本机可以通过指纹验证主机真实性)
      * ```The authenticity of host 'host (***.***.***.***)' can't be established.RSA key fingerprint is 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d.Are you sure you want to continue connecting (yes/no)?```
    * 本机将密码用远程主机给的公钥加密时候,给远程主机
    * 远程主机用私钥解码登录密码验证完成之后登录
  * 基于秘钥的登录
    * 客户端将公钥上传到服务器(ssh-copy-key)登录时向服务器发送请求,服务器向用户随机发一串字符串,用户用自己的私钥加密之后,传到服务器,服务器使用之前上传上去的用户公钥,如果解密成功,就允许用户登录
      * 上传服务器的过程,ssh-copy-key指令实际做的是,将公钥内容(本地id_rsa.pub)的内容append到服务器的~/.ssh/authorized_keys中,重启服务之后即可成功
    * 整个过程不需要上传密码
  * 如何生成ssh秘钥对?
    * 秘钥总是成对出现,一公一私
    * 使用RSA/DSA/ECDSA等方法
  * 
* MyConfig

``` py

Host eva
	HostName 101.6.64.169
	Port 22
	User zhaotianchen
	ProxyCommand ssh zhaotianchen@101.6.64.67 -p 42222 -W %h:%p
Host fpga
	HostName 101.6.68.236
	Port 22
	User ztc
	ProxyCommand ssh zhaotianchen@101.6.64.67 -p 42222 -W %h:%p

Host 205
  HostName 101.6.64.67
  User zhaotianchen
  Port 42222

Host eva*
  HostName %h.nics.cc
  User zhaotianchen
  ProxyCommand ssh zhaotianchen@205 nc %h %p

Host *-eva10
  User zhaotianchen
  ProxyCommand ssh -p 32222 zhaotianchen@eva10 nc %h %p

Host proxy-eva10
  HostName eva10.nics.cc
  Port 32222
  User zhaotianchen

Host ztc.eva10
  HostName foxfi-eva10
  User zhaotianchen
  ProxyCommand ssh -p 32222 zhaotianchen@eva10 nc %h %p

Host *.eva*
  HostName %h.nics.cc
  User zhaotianchen
  ForwardAgent yes
  ProxyCommand ssh zhaotianchen@205 nc %h %p
```

* [使用 SSH config 文件](http://daemon369.github.io/ssh/2015/03/21/using-ssh-config-file)
  * 仅支持逻辑通配 */?/!
  * IdentityFile指定秘钥位置(默认.ssh/)
  * %P (Port) %h(Host)
  * nc表示这个是中间跳板(~~不要停下来啊~~)
* [Multi](https://www.cyberciti.biz/faq/linux-unix-ssh-proxycommand-passing-through-one-host-gateway-server/)
* ```scp FILE ztc.eva0:~/```