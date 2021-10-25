---
layout:     post                    # 使用的布局（不需要改）
title:      Linux常用命令笔记              # 标题 
subtitle:   学了忘，忘了学(Learning and then Forget, then Forgot to Learn)     #副标题
date:       2024-01-01              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-term.png   #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:    
    - 常用                           #标签
    - 学习笔记
    - Linux
    - 环境配置
---

# Ubuntu(Linux) Commands
* lsb_release -a 查看**系统**版本
* ```mv```命令没有-r,直接加"/"就默认地递归移动文件
* uname -a 查看**内核**版本
* ping google.com
    * -f 极限检测
    * -s packet_size
* lscpu - 查看cpu
    * top 查看cpu使用率
* ps -p $$ (通过查看进程的名字)
    * 用 echo %SHELL (比如在zsh里面也会显示是bash)
* apt的使用
  * 检查某个东西是否有 ```apt-cache search filezilla```
  * 安装 ```sudo apt-get install filezilla```
  * 卸载 ```sudo apt remove filezilla && sudo apt autoremove```
* tar -zxvf xxx.tag.gz
    * -jxvf xxx.tar.bz2
    * 其实都用　```tar -xvf```就行
    * 压缩用　```tar -zcvf XXX.tar.gz XXX/```
    * unzip xxx.zip
    * cpio -idmv < xxx.cpio(解压到当前目录)
* 安装一下rar和unrar
  * ```unrar x xxx.rar```正常的操作方式
  * args中国的x换成e是全部解压到当前目录
* vivado -source xxx.tcl
* 退出qemu: CTRL+A + X
* 命令行挂载一系列操作：
    * sudo fdisk -l 查看自己的盘是/dev/sdx (未挂载的也会看见)
    * df -kh 查看挂载设备的位置
    * sudo mount -t /dev/sdx /mnt
        * 常用的挂载目录 /mnt  /media (在底下可以自己建立文件夹)
        * 最后一个参数如果填写自己的目录有会报 Directory Not In /etc/fstab
    * 用完之后umount就好
* Ubuntu ssh 获得图形界面
    * 在Server端开启转发X界面
        * /etc/ssh/sshd_config
          * 保证X11Forwarding为yes
        * 重启一下ssh服务 
          * cd /etc/init.d这个目录下执行 ./ssh restart
    * client 开启服务
        * /etc/ssh/ssh_config
            * ForwardAgent yes
            * ForwardX11 yes
            * ForwardX11Trusted yes
    * 下次登录的时候ssh后面加上 -X/-Y命令
    * 在本地（client） xhost +服务器IP
    * 然后我就可以了，可能和服务器之前的配置有关系
        * （正常这里还需要修改一下环境变量中的DISPLAY指向 “client'sIP：0.0”）
        * 后续一次我的配置过程当中发现：由于X11Display Offset设置为10，在环境中把DISPLAY=:10.0然后好了，很神奇
            * 参考了[这个链接](https://askubuntu.com/questions/61690/ssh-x-xt-error-cant-open-display-0-0)
    * 可以用xclock测试是否成功
* scp -r IP_HOST:/home/xxx /local_directory
    * 实施证明vscode remote能吊打它
    * 好的我收回上面的话　记得这个-r一定要加，不然会不能补全服务器的目录
* vscode git 免密码push
    * 在 .git文件夹中的config的url一栏 http://username:passwd@github.xxx.git
        * 注意这里是用户名不能是邮箱，不然会解析错误
* vscode免密码ssh登录
    * 首先创建秘钥： ```ssh-keygen -t rsa -b 4096 ```
    * 拷贝秘钥到服务器 ```ssh-copy-id -i id_rsa.pub ID@IP``` （如果这里不明确ID默认就是你的本机ID）
    * 然后就可以登录了
* 命令行切换语言
    * sudo vim /etc/default/locale　把所有的zh_CN改成en_US
    * 文件是只读所以必须要sudo，重启或者挂起登录之后生效
    * （需要这个是因为petalinux的qemu居然设置中文会出bug）
* 建立用户
    * useradd -s 'bin/bash' -m -G adm,sudo %username  (带了sudo权限)
    * passwd %username
    * 给他sudo权限
      * 在su情况下修改 /etc/sudoers 权限为可写
      * 然后加上一行 ```$USERNAME ALL=(ALL) ALL```
      * 再把权限改回来就可以了
* 换源
    * /etc/apt/source.list
* 切换用户
    * su %username
* 为文件切换用户
    * chown %user:%group
* 网络相关
    * ifconfig
        * enps031f6　不是eth0
    * ip a （IP ADDR）　
    * ip route show
    * ping 8.8.8.8  
    * ```sudo service networking restart```
    * [链接](https://www.tecmint.com/linux-network-configuration-and-troubleshooting-commands/)
* 找文件 find
    *   ```find [Directory] -name 'filename' ```  这里的filename 超出的部分要加*  
    *   -iname 以不区分大小写
        * ```find: paths must precede expression:``` 没有打引号导致把*错认为是当前目录的意思
* ll(ls -l)命令可以查看权限和软链接     
    * ```cat /var/log/Xorg.0.log | grep EE```
    * ll与find相比不需要加上　**    
* 寻找缺少的Dependency对应的库
    * ```dkpg --search *.so```
* 对于一个指令(比如startx)如果需要分析它的过程
    * 在/usr/bin目录寻找该文件
* ```ln -s A B```
    * link　软链接 Create B -> A
        * AB可以均为目录，把底下所有文件都链接过去
    * 创建新的只能是文件，如果要连接文件夹需要先新建，再链接
    * 采用-snf 选项进行修改(覆盖)
* ps查看进程
    * args:
        * -a 等价于-e　列出所有
        * -w　显示加宽
        * -au　包含详细信息
    * ```ps -aug | grep xxx```
* 安装```sudo apt-get install sysstat```
  * 用```iostat -s 1 -kh```
    * ```-kh```的含义其实单位在```df -kh中同理```
* 查看当前系统的所有服务
    * *服务*　支持系统运行的一些必要进程的集合，本质还是进程
    * ```service --status-all　｜ grep running```  
    * 但是我们实际上用的不是这个，应该是systemctl
        * ```systemctl | grep running```
    *  查看系统启动时候的服务　```systemctl list-unit-files | grep enabled```
    *  按照资源分配来排序服务  ```systemd-cgtop```
    *  ```pstree```层次列出
    *  查看某个服务的运行状态```service xxx status```
* 反复刷新某个命令 ```watch -n 0.5 -d $COMMAND```
    * ```－d表示hightlight变化的部分W```
*  生成分辨率
      *  ```gtf 1920 1080 60 -x```
      *   Modeline "1920x1080_60.00"  172.80  1920 2040 2248 2576  1080 1081 1084 1118  -HSync +Vsync
*   修改分辨率
    * 如果xrandr有东西　```xrandr -s 1920x1080```
* rsync 命令　带不带“/”　差距很大  
    * 一个是复制目录，一个是把目录里面的东西复制过去　(如果搞错了就会把一大堆乱七八糟文件夹直接复制到根目录地下)
* 通过命令行写文件方式　　```echo "XXX" >> ~/.bashrc```
* **REGEX** 正则表达式
    * [参考链接](https://github.com/ziishaned/learn-regex/blob/master/translations/README-cn.md)
    * 规则 [](https://github.com/ziishaned/learn-regex/blob/master/img/regexp-cn.png?raw=true)
    * Re规则和find -name的显著区别有： 对于名字比待匹配字数多出来的不用加*
    * "^"从开始开始匹配  "￥"从末尾开始匹配 
    * "." 任意单个字符  "*"之前的字符出现0或者更多次  "+" 前面的字符>0个的字符 "?"表示前面的字符出现0或者是1次
    * 字符集： "[]" 字符种类  "[^]"禁用的字符种类
    * {}量词：  ```[0-9]{2,3}``` 匹配2到3位0~9的数字
    * **Examples**
        * ```[tT]he``` -  匹配的是the/The    
        * ```c.+t``` - c开头t结尾中间有字符
        * ```[&c]ar```
        * 
* 安装并开启ssh服务
* ldd　$EXEUCATBLE　查看运行文件所用到的动态链接库

  * ```sudo apt-get install openssh-server ```
* OPENCV
    * 查看版本　```pkg-config --modversion opencv```  
    * [卸载](https://www.cnblogs.com/txg198955/p/5990295.html)
        * 进入Build或者Release目录　```sudo make uninstall```
    * 多版本共存可以几个版本都源码build，然后进入目录用```make install或者是make uninstall来选择版本```
* rename command
    * 可以使用通配符，也可以使用正则(re)
        * **通配** （默认配置，比如grep默认也是用通配）
            * ？替代单个字符
            * * 替代任意个字符
    * 由于mv指令不能批量
    * *template*  ```rename [-options] "s/oldname/newname" flie```
        * 这里的file写成"."表示当前目录下的所有文件
    * *args*
        * -n 测试，并不是实际
        * -v (verbose)
        * -f (force)覆盖本地存在的文件  
    * *Examples*
        * 替换部分： ```rename “s/AA/aa/” * ```
        * 修改后缀  ```rename "s/.html/.php/"```
        * 添加后缀  ```rename "s/$/.txt/"``` // $表示文件末尾
        * 删除部分  ```rename "s/aa/ /"```
* 虚拟内存
    * 参考了[博客](https://blog.csdn.net/qq_38701476/article/details/83042668)
* updatedb 用于更新locate指令所调用的数据库
    * locate命令　比find执行更快
* Ubuntu目录下的lost+found/在非正常关机的时候会把一些文件放进这个文件夹供数据恢复用，大小可能有好几个Ｇ
    * 这种情况在板子不把系统shutdown直接断电的时候经常会出现
* base64编码解码
  * ```echo 'a-suozhang.xyz' | base64```
  * ```echo $BASE^$_STRING | base64 -d ```
* 开机自启动
    * 最脑瘫的方式是命令行*gnome-session-properties*打开图形界面设置
* vim编辑之前忘记sudo无法保存
  * 键入```:w !sudo tee % ```
* ```tree -s```显示文件大小
* 在文件夹下搜索
	* ```ll | grep -i XXX```
		* 其中的-i忽略大小写
	* ```find -iname "*XXX*"```
		* find命令中的两个星号还比较关键
* 查看当前目录下的文件里的内容,用grep
	* 和查看当前目录下的文件名字用 ```ll | grep -i xx```类似
	* 如果需要递归 ```-r```
* ```readlink -f XX```
  * 查看连接的指向
* 查看目录的磁盘占用 ```du -sh```
	* s的作用是不让子目录也输出
* privoxy
    * (在eva服务器上挂代理会影响tunet的登录，先把http(s)_proxy设置为空再tunet)
    * [Privoxy的配置教程](https://docs.lvrui.io/2016/12/12/Linux%E4%B8%AD%E4%BD%BF%E7%94%A8ShadowSocks-Privoxy%E4%BB%A3%E7%90%86/)
    * 首先```sudo apt-get install privoxy```
    * 编辑 ```sudo vim /etc/privoxy/config```
        * 搜索```listen-address```保证```listen-address 127.0.0.1:8118```存在，将来的http代理将在8118端口
        * 将``` #forward-socks5t / 127.0.0.1:1080 .```接触注释(注意结尾的“.”)
    * 重启Privoxy
        * ```systemctl restart privoxy```
        * ```systemctl enable privoxy```
    * 在```~/.bashrc```中添加
        * ```export http_proxy=http://127.0.0.1:8118```
        * ```export https_proxy=https://127.0.0.1:8118```
    * 用```curl www.google.com``` 来测试是否成功
* git config
    * ```git config --global http.proxy socks5://127.0.0.1:10808```
    * 在```~/.gitconfig```
* Crontab 定时运行
* 由于distributed训练有的时候会出现进程杀不干净的情况，所以需要手动清一下
	* ```ps aux | grep -i awnas``` - 这样找到Id号然后kill掉
* 添加sudo用户的时候，用```sudo visudo```命令这样会更加安全一点,会做检查



---

### Grep

* grep (Global search for RE & Print)
    * 基本用法： ```grep -X MATCH_PATTERN filename1 filename2```
    * 还可以在shell中直接使用 ```COMMAND | grep -X XXX```
      * 从数据流中匹配
      * 比如用 ```cat a.txt | grep X```,就等价于```grep X a.txt```
    * *注意只有使用了-E的时候才是使用正则，默认是直接使用逻辑通配，不应该加```*NAME*```用```"NAME" or NAME```即可*
    * args 
        * -v 反选(打印除了匹配的)
        * --color=auto
        * -E 使用正则表达式 （或者用egrep）
        * -o 只输出匹配到的内容，而不是整行
        * -n 输出包含行数
        * -i 忽略大小写
        * -c 匹配的个数
        * -r 递归查找 （此时的后面的文件名可以是文件夹名）
* 在grep的正则中，首先空格需要转意
* grep命令行中的正则是默认贪婪的，需要把原来的```*改为.*?```来阻止贪婪匹配
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200209153345.png)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20200209153417.png)
  * 可以看到改动了之后只有完全匹配的打印了出来

* 逻辑通配符
  * 是Shell内置的功能，对应Re
  * find和grep默认采用的格式
  * 常用
    * ```*``` - 匹配任意字符串
    * ```？``` - 匹配单个字符
    * ```[]``` - 匹配括号内字符（类似re）

### Sed

* 批量修改命令sed
  * ```sed -i "s/Re0/Re1/g" a.txt``` 按照正则的方式替换
  * 对多文件批量替换  ```sed -i "s/old_string/new_string/g" \` grep old_string -rf ./ \` ``
* 批量重命名 rename 与上同理
* diff两个file就是``diff FILE_A FILE_B```

### awk

> 对应着Shell工具，awk对应逐行的处理，sed用作编辑，grep用作查找

awk接收一个模式串以及一个代码块

- 使用方式

awk命令原始的使用方式可以是`awk $pattern $filename`，但是一般使用管道，相当于用前面命令的输出作为文件流给awk命令

主要的pattern是引号内，大括号内表示操作,`~`表示服从某个规则
最常用的指代： `$0` 表示整行，`$N`表示第N个代码块的部分，默认是用空格作为分隔
测试：`ll | awk '{print $3};'`

使用`$1 ~ /something/`来匹配某个pattern




---


# Bash

> 参考了[简书-Bash](https://www.jianshu.com/p/e1c8e5bfa45e)

> 以及[This Tutorial](https://linuxhint.com/bash_scripting_tutorial_beginners/#b4)

### Basics

* Shell本身就是在OS和用户之间的一个命令解释器
* 起手 ```#！/bin/bash```选择bash作为解释器
* 可以直接 ```bash hello.sh```或者先赋予可执行权限```chmod a+x ./hello.sh```然后直接```./hello.sh```
* 变量可以直接定义（类似Python）但是注意“=”左右不能有空格（类似bashrc嘛）
* 本质上变量都是字符串
  * 只有当变量为纯数字的时候才可以被认为是“数字变量”，否则都是字符串  
  * ```d=${c/BB/12}```可以替换c中的BB为12
  * 注意数字变量和字符变量的区别
    * 数字变量用 ``` $a -ge $b``` 这样的模式来比较
    * 字符串变量用 ```$a = $b```这样的链接
* 引用变量用 ```${my_var}```大括号也可以不加
* 单引号内部的内容会被全部输出（不会带转义）但是双引号之间的可以
  * echo出一个变量要么不用引号要么双引号
* ```${var:-DEFAULT}``` 如果var没有被声明, 或者其值为空, 那么就以$DEFAULT作为其值 *  

``` bash
N=$1

use xxx ${N:-1}
* 字符串相等或者不相等一定要加空格不然会出现奇怪的现象 ``` $a != "b"```
```

* 数字运算
	1. expr ```r=`expr 4 + 5` ```
		* 注意expr后面要加空格
	2. $(()) ```echo $((2+3))```
	3. $[]  ```echo $[2+3]```
	4. let n=2*3
  
* 条件语句

``` bash
if [ condition1 ]
then
    command1
elif [ condition2 ]
    command2
else
    commandN
fi
```

```if [[ $a == "a" && $b != "c"  ]]```
* 如果有用到了&&的话一定要用两个]]

* 如果在这里是判断字符串相等的话 ```$a == $b```,注意有空格，等号一个两个都可以(但是貌似对双中括号来说情况不一)

* 循环语句

``` bash

for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done

while [ condition ]
do
    command
done

```

* 数组

``` bash

arr=(1 2 3 5 8 13)

total={#arr[*]}
echo "Num of Elements:"${total}

echo "Array Values:"
for var in ${arr[*]}; do
        printf "%d " ${var}
done

echo "Array Value with Index"
for key in ${!arr[*]}; do
        printf "%d: %s\n " ${key} ${arr[${key}]}
done

```

* 循环，range

```for i in $(seq 0 ${END); do echo ${i}; done ```

``` bash
for ((int i; i<10; i++)) do
    echo $i
done
```

* 遍历某文件夹下的文件

``` bash
for filename in ./*.mp3; do
    echo $filename
done
```

### 字符串

* ${#string}                                      $string的长度 
* ${string:position}                              在$string中, 从位置$position开始提取子串至屁股
* ${string:position:length}                       在$string中, 从位置$position开始提取长度为$length的子串     
* ${string/substring/replacement}                 使用$replacement, 来代替第一个匹配的$substring
* ${string//substring/replacement}                 使用$replacement, 来代替最后一个匹配的$substring
* ```expr match $string "\(re\)" ```

* 替换名字
	* 比如我需要将ms.tex转化为ms.aux
	* ```${FILE/.tex/.aux}``` - (等价于s.replace())
* Example - mp4 convert mp3
* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20200715090430.png)

### 管道

- 最简单的使用例子 ```ll | grep -i something```
- 输出到文件  `curl www.baidu.com > baidu.log`
- 当文件内容很多的时候，可以用less命令来建立管道，这样可以翻页浏览，而不是打印出很多 `grep train_acc train.log | less`
- 在管道中使用sed替换  `curl www.baidu.com | sed 's/baidu/google/g' | grep google`
- `wc -l` 列出行数sum

作用是下载下baidu.com的source code并将其中所有的baidu替换为google，然后在索引到其中所有的google
管道从左向右运行
