---
layout:     post                    # 使用的布局（不需要改）
title:      网络安全笔记           # 标题 
subtitle:   真就一节课没听过…        #副标题
date:       2020-11-22            # 时间
author:     tianchen                      # 作者
header-img: img/diffusion/disco-8.png  #这篇文章标题背景图片  
catalog: true                       # 是否归档
tags:                               #标签
     - 课程
---

# 网络信息安全笔记

## Chap1 Intro

* 信息安全的4个目标： 保密/完整/可用/合法
* 常见威胁: 授权侵犯 / 假冒攻击 / 旁路控制 / 木马  / 媒体废弃 /
* 攻击的分类： 主动/被动
	* passive: 监听和政策
	* active: 恶意篡改数据流
* OSI - 7层
	* 物理 - 数据链路 - 网络 - 传输 - 会话 - 表示 - 应用
	* Internet 4层: 数据链路 - 网络 - 传输 - 应用
* X.800标准的5x安全服务/8x安全机制
	* 服务(x5): 认证，访问控制，数据保密性，数据完整性，不可否认性
	* 安全机制(x8): 加密，数字签名，访问控制，数据完整性，认证交换，流量填充，路由控制，公证
	* ***安全服务通过安全机制来实现安全策略
	* 非常关键的表格
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122213638.png)
* 网络安全参考模型
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122213740.png)
* 网络访问模型
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122213815.png)

## Chap2/3 (Less Important)

* IPV4 - 32 bit - 192.168.0.1 - 8-bit per stage
* IPV6 - 128 bit - 2000:xxxx:xxxx:...  - 16 bit per stage
* MAC - 48 bit - FF:FF:FF:FF:FF:FF
* 常用端口号
	* HTTP - 80 - Hyper Text Transfer Protocol
	* FTP - 20/21
	* Telnet - 23 - 远程登录服务
	* pop3 - 110 - 收邮件 - post office protocol
	* SMTP - 25 - 发邮件 - simple mail transfer protocol
	* SSH - 22 - 远程登陆以及其他网络服务安全协议  - Secure Shell
	* DNS - 53 - 域名解析到IP - Domain Name System
* Why进行网络地址转换(NAT) - IP地址短缺
* ARP(Address Resolution Protocol)
	* 局域网中的32bit IP地址转化为48b的物理MAC地址
* Why UDP less secure than TCP
	* UDP没有交换握手的过程

### Chap4

* 单钥 - 对明文信息处理方式 - 分组密码 / 流密码
	* 分组密码都包括扩散和混淆
* DES的分组长度64位，密钥长度56位
* AES分组长度128位，密钥128/192/256 
* 加密安全性取决于密钥的保密，加密算法可以公开
* 加密轮数是否越多越好，密钥是否越长越好，经过2个算法串联加密不一定更安全
* 分组密码的5种加密模式
	* 电码本模式 - ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122215335.png)
	* 密码分组链接模式 - ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122215447.png)
	* 输出反馈 - ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122215551.png) 
	* 密码反馈 - ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122215635.png)
	* 计数器模式 - ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122215719.png)

### Chap5 

* 双钥 - 基于数学难题构造
	* 多项式求根，离散对数（Diffe-Hellman），大整数分解（RSA），背包问题，二次剩余问题，模N的平方根
* RSA 计算公式
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122220156.png)
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122221923.png)
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201122222444.png)
	* RSA 可以看作是分组密码体制
	* 加密的时候用的是双方的公钥，解密的时候用的是自己的私钥
* RSA也可看作是分组密码
* 双钥体制用: 接收者的公钥进行加密，解密的时候用接收者的私钥

### Chap6

* Hash函数不可逆，加密函数可逆,hash函数一般不需要密钥
* 杂凑函数的性质： 混合变换，抗碰撞攻击，抗原像攻击，实用有效性
* 消息认证码mac - 有密钥控制的单项杂凑函数,杂凑值不只和输入有关还和密钥有关，只有有密钥的人才能计算出杂凑值
	* MAC=C_K(m) - M是一个变成消息，K是密钥，Ckm是定长的认证符
* 消息检测MDC： MDC是没有密钥认证的单项杂凑函数，杂凑值只与输入字符串有关，任何人都可以计算，没有身份校验的功能
* MDC和MAC都可检测接收数据的完整性
* MD5的输出长度 - 128b
* SHA-1的输出长度 - 160b
* 提供安全功能 
* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201124082038.png)

### Chap7

* 数字签名的性质
	1. RX可以确认认证发放的签名，但是不能伪造
	2. TX发出签名的消息发给对方之后，就不能否认他发出签名的
	3. RX对收到的签名消息不能否认-有收报认证
	4. 第三者可以确认收发双方之间的信息传送，但是不能伪造
* 数字签名分类-确定性/随机化
* RSA签名基于 - 大整数分解
* ElGamal基于 - 离散对数
* 签名的时候，用签名者的私钥。验证的时候用的是公钥
* Diff-Hellman	不能用作数字签名
* 单钥不能用作数字签名
* 数字签名与双钥加密的区别
	* 数字签名用签名者的私钥进行签名，用签名者的公钥验证
	* 双钥发送者用接收者的公钥进行加密，接收方用自己的私钥进行解析

### Chap8
* 协议的3要素: 有序性，至少两个参与者； 通过执行协议必须完成某个任务
* 密码协议按照密码协议功能分类
	* 密钥建立协议
	* 认证建立协议
	* 认证的密钥建立协议
* 中间人攻击: Marllory不仅能够窃听AB之间的交换信息，而且能够修改删除信息，生成全新的消息，BA对话时候，M可以冒充B(发送方)
* Diffie-Hellman
	* 对称密钥体系
	* 在不安全信号创建起一个密钥，密钥交换协议
* Diffie-Hellman协议的密钥交换过程(K/K'就是共享的密钥)
	1. A选择一个随机的大整数，向B发送 X=g^x(mod n)
	2. B选择一个随机的大整数, 向A发送 Y=g^Y*(mod n)
	3. A计算 K=Y^x(mod n)
	4. B计算 K'=X^Y(mod n)
* Diff-Hellman不能抵抗中间人攻击
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201124082935.png)
* Diffie-Hellman不能作为数字签名
* 大嘴青蛙协议和Yahalom安全协议的思想：
	* 大嘴青蛙协议：AB与T共享一个密钥，A需要传两条信息可以将一个会话密钥发送给B
	* YahaLom: B首先与T接触，而T仅向A发送一条信息

### Chap9

* PKI - 遵循标准的利用公钥理论和技术建立的提供安全服务的基础设施
	* 由证书机构(CA)负责发放和管理数字证书
	* 注册机构(RA)按照特定政策与管理规范对用户资质审查
	* 证书发布库 - 开放式查询公共信息库
	* 密钥备份与恢复 - 提供密钥备份与恢复机制
	* 证书撤销  
	* PKI应用接口 - 使用户能方便的使用加密，数字签名等安全服务
* 数字证书
	* 一个用户的身份以及其所持有的公钥的结合，结合之前用一个可信的权威机构CA来核实用户身份，由该机构对用户身份以及其对应公钥结合的证书进行数字签名，证明签名有效
* X.509的数字证书格式
* CA来签发证书，CA用私钥进行签发； 验证的时候用CA的公钥进行签发
* 数字证书的作用
	* 证明网络实体在特定安全应用的相关信息
	* 解决公钥可信性问题

#### Chap10

* 4x加密方式： 1. 链路加密 2. 节点加密 3. 端到端加密 4. 混合加密
* 密钥种类
	* 基本密钥: 由用户选定或者系统分配，在较长时间由一对用户专用
	* 会话密钥: 两个通信终端用户在一次通话之中所用的密钥
	* 密钥加密密钥: 用于对传输的会话或文件密钥进行加密用到的密钥
	* 主机主密钥: 对密钥加密密钥进行加密的密钥
	* 工作密钥: 在不增大更换密钥工作量的条件下扩大可使用的密钥量
* 密码协议
	* 密钥建立体系
	* 认证建立体系
	* 认证密钥建立体系
* 好的密钥
	* 随即，等概率
	* 避免使用特定算法
	* 满足一定的数学关系
	* 易记难猜
	* 采用密钥杂凑技术
* 软件加密 - 任何加密算法都可以软件实现，灵活轻便。在主流OS上都可以使用，但是速度慢，安全性有风险
* 硬件加密 - 加密速度快，硬件安全性好，硬件易于安装

#### Chap11

* 无线网络的威胁
	1. 窃听
	2. 通信阻断
	3. 数据注入和篡改
	4. 中间人攻击
	5. 客户端伪装
	6. 接入点伪装
	7. 匿名攻击
	8. 隐秘无线信道
	9. Client2client攻击
	10. 服务器识别符的安全问题
	11. 漫游问题
- GSM的安全威胁
	1. GSM标准只考虑了移动设备和基站之间的安全问题，而基站之间没有加密措施，所以Kc以及SERS在网络中铭文传输
	2. Ks长度是48b，截取RAND和SERS之后容易攻破，使SIM卡的复制成为可能
	3. 单向身份认证，网络认证用户，用户不认证网络
	4. 缺少数据完整性检验
	5. 跨区切换过程可能泄露用户秘密
	6. 用户无法选择安全级别
- 为何挑战值一定是一个随机数，不能是一个常数
	1. 防止强力攻击，128b的长度，完全随机组合，即使黑客知道算法，也难以获得RAND SENS
- 3G网络的保密认证协议
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201215225310.png)	
- 3G better than 2.5G
	* 实现了用户与网络之间的认证
	* 用户与网络之间的会话密钥
	* 保证了密钥的鲜活性

#### Chap12

* 防火墙的类型以及优缺点
	* 静态包过滤防火墙 - 对性能影响小，成本低； 安全低，缺少感知能力，容易IP攻击，创建访问控制规则困难
	* 动态包过滤防火墙 - SMP技术同样对性能影响小，安全性稍好一些，感知能力++； 
	* 电路级网关 - 性能比应用型更好，切断了外部网络与防火墙的直接链接，比包过滤防火墙安全性更好； 无法抵抗应用层攻击，增加新的程序需要修改的代码以及电路多
	* 应用级 - 安全性高，强大认证，日志，规则配置简单； 灵活性差，性能不高
* IDS类别以及特点
	* 基于网络的入侵检测系统(NIDS): 成本低，攻击者转移证据困难，实时检测,能够检测未成功的进攻，操作系统独立
	* 基于主机的入侵检测系统(HIDS): 监视特定活动，用于加密环境，不需要额外硬件，近实时的应答
	* 分布式入侵检测系统 
* VPN类: 远程访问VPN，网关-网关VPN
	* ![](https://github.com/A-suozhang/MyPicBed/raw/master//img/20201215231300.png)

#### 5G

* 5G对网络安全的挑战：新业务场景，新技术特征，新商业模式，多种接入设备，极强的隐私保护
	- 增强移动宽带： 更高的处理器性能，支持外部网络二次认证，网络切片安全
	- 高可靠低延时链接： 低时延的安全算法，协议； 边缘计算安全架构； 隐私关键保护
	- 海量链接物联网业务: 轻量化安全，群组认证， 抗ddos
* 5G安全架构:
	* 5G独立组网中，“基于服务的架构安全域”
* 5G新的防护框架
	* 加入了： 3GPP接入，切片和虚拟网元安全，网络开放接口安全以及安全管理
	* 用户平面完整性保护，避免了4G中DNS欺骗
	* 对IMSI加密，利用集成电路卡，对空口中的SUPI进行加密，5G网络不能获得用户的标识位
	* 采用IPsec/TLS协议来保证NE/NF间的安全，IPSec在3GGPP网元之间，TLS用在HTTPS中
	* 端到端安全，添加安全边缘防护代理
	* 5G网络接入安全： 认证协议拓展，密码算法更新，隐私信令用户面保护，密码管理体系
* 5G的加密技术
	* 用户控制面
	* 用户面和信令加密
	* 支持层次化的密钥派生体制
	* 动态认证机制
	* 切片隔离技术
	* 用户面完整性验证

### Critical

* 前面的试题基本是原题
* GSM的安全性欠缺还是会考
* 5G哪里的安全性增强
