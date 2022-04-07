---
layout:     post                    # 使用的布局（不需要改）
title:      建立博客的过程               # 标题 
subtitle:   Where did This Blog Form #副标题
date:       2020-07-04              # 时间
author:     tianchen                      # 作者
header-img:  img/bg-term.png   #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 测试
    - 环境配置
    - 博客
---

# 尝试建立一个博客

## 我是个穷人，买不起服务器
## 还是条懒狗，懒得去维护
## 还是个笨比，学不会前端
### 采用了github.io加上别人现成的模板的方式搭建
* 参考了这位大哥的[博客](https://www.jianshu.com/p/e68fba58f75c#Rename)
    * 实在是有点牛逼，他讲的太清楚了,不需要参考别的了
    * 只有一点就是如果建立repo之后打开网址404
        * 自己的名字里面有大写字母会被映射到小写这个没有关系
        * 按照他文章里面的方法　在根目录下建立一个CNAME并且将网址(a-suozhang.github.io)复制进去，然后就好了

## 图床
* 选择了[PicGo](https://github.com/Molunerfinn/PicGo) + Github的解决方案
    * 跨平台
    * 白嫖
* 如果下载Appimage启动的时候报错
```there is no application installed for appimage application bundle files```
    * 修改运行权限即可
* 启动之后会有一个方形的小窗口,右键查看详细
* 需要在Git上建立一个repo,并且从这个[链接](https://github.com/settings/tokens)获取一个Token
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191021090349.png)
* 设置Github为默认图床,并且输入配置(用户名+Token等)
  * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20191021090245.png)
* 之后有两个方式上床(雾)
    * 剪贴板上传,点开图床右键
        * 还需要安装一个xclip ```sudo apt-get install xclip```
    * 双击图床,可以上传图片,上传完成之后会直接获得图片链接(且是以markdown的格式)
* 上面这种链接经过自己的测试,在本地不能渲染,但是在github的markdown可以渲染,**但是在JerkyII的博客里面是没有办法渲染的**
    * 查阅了一些文章,将上面链接里面的/blob改成/raw即可解决该问题
    * 修改图床软件的自定义域名里面的blob为raw
* *如果有的时候莫名其妙传不上去了，首先重启一下picgo看看好没好（有时候这样真的就解决问题了），然后上git看看自己token还在不在*

## 压缩图片
* Win下选择了腾讯的[智图](https://zhitu.isux.us/index.php/preview/install)
* 这个东西安装的时候真的有点蠢，点开智图的软件会启动Imagedisk的安装，不能按照默认配置安装(安装文件也不说一声，只有官网上有)
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190925224324.png)
* 按照图中配置应该就可以启动了，把图片拖进去就可以了
    * 注意可以勾选**替换原图**
* 打开zhitu.exe之后有时候会跳出安装ImageDisk来(我已经装了啊)
    * 我猜测原理是检查执行文件路径(环境变量)中有没有ImageDisk的执行文件
    * 如果嫌麻烦直接把安装产生的ImageDisk可执行文件复制到智图的根目录底下就能跑了

## 本地调试

### Ubuntu 
1. 在[JekyII官网](https://www.jekyll.com.cn/docs/installation/ubuntu/)上获得了安装指南
```
sudo apt-get install ruby-full build-essential zlib1g-dev
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
gem install jekyll bundler      
```
2. 进入$PATH_TO_YOUR_BLOG_REPOSITORY
```
jekyll s
```
3. 这时在[localhost 4000](http://127.0.0.1:4000/)上能看到blog

### Windows
1. [官网](https://www.jekyll.com.cn/docs/installation/windows/)
2. 上Ruby官网安装包（带Dev Kit的）
    * 在安装程序进行完之后会跳出一个像cmd一样的安装程序，把那个里面东西也安装了吧
3. 在Start Up Menu点开命令行安装jekyll
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190908140215.png)
输入```gem install jekyll bundler```
4. 和Ubuntu一样，进入你的项目文件夹，运行 ```jekyll s```应该起了

## Math Support
* 尝试公式的支持
* VScode下安装 Markdown+Math插件，同样适用CTRL+SHIFT+V即可看到公式

$$ x^100 $$

* 参考了这篇[博文](http://cyukang.com/2013/03/03/try-mathjax.html)
1. 本地调试安装Kramdown
```
gem install kramdown
```
2. 在 .config.yml 里面将markdown改为kramdown
3. ⭐~~最关键的，加载MathJax服务，我是在这里，插入了代码~~
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190908231632.png)
```
<script type="text/javascript"
 src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```
4. 上面那个好像么得作用...在这个地方加这段代码
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190908232728.png)
```
// mathjax 
<script>
  (function () {
    var script = document.createElement("script");
    script.type = "text/javascript";
    script.src  = "https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML";
    document.getElementsByTagName("head")[0].appendChild(script);
  })();
</script>
```

* ~~能够在本地渲染下看到公式，但是push过去还是不行🤔~~ 事实证明第二段代码起作用
* 🔣 Note That The ```$Formula$``` is supported by Markdown But Not Online
    * $x^３$ 这种表示的是inline的公式，本地markdown渲染支持，但是云端貌似不支持
    * 云端支持的貌似是这种形式的 $$ x^2+y^3=\lambda $$
* 测试样例
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190909091856.png)
* 然而这种是两者都能接受的，是单独的居中的公式　```$$ x^3+y^4 $$```
* **解决方案**：在本地先使用　```$FORMULA$```书写并测试渲染，push之前再改成```$$ FORMULA $$```
$$ F(x) = P\{ X <= x \} = \int_{-\infty}^x f(x)\,dx $$
* 这个是网页上支持的另起一行的公式书写(但是本地markdown不支持)
\\[ f(x,y,z) = 3y^2 z \left( 3 + \frac{7x+5}{1 + y^2} \right). \\]]


## 公式的解决方案

> 采用Mathjax进行渲染会导致本地与云端格式的不同，采用了第三方平台渲染成SVG的方式来实现

* ```![equation](https://latex.codecogs.com/svg.latex?\frac{1}{3}{sin(5)})```
* ![equation](https://latex.codecogs.com/svg.latex?\frac{1}{3}{sin(5)})


## Comment Support

> 选用了Gittalk,因为作者的Tempelate里面有这一条
1. 在github个人设置中获取token

![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190924105555.png)
![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190924105631.png)
* 建立一个Oauth App
* 内容按照图中填写
  * Homepage URL填写Repo的地址
  * Callback URL填写域名地址
* 在作者tempelate的config.yml中加入如下所示的代码
  * 注意repo的域名即使你买了自己的域名,这里也要填写你的git repo的名字
  * 两个token来自github的账户设置
* ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190924105332.png)
* ```这一步完成之后应该不再会出现 "Error Not Found"```

2. 添加isssue权限
   * 如果上一步完成之后显示"Issue Disabled" 
   * 进入blog对应的repo-Settings-features并赋予权限
   * ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20190924110445.png)

* ```理论上这样设置完了之后就可以正常的进行评论了,评论会在博客的repo下面建立一个issue```


## 域名

* 在[阿里云 - 域名控制台(https://dc.aliyun.com/basic/domainDetail.htm?spm=a21av.7665006.a2a47.d1176444209.460b2a2d5fZSWr&dtoken=TlpOZe6X3jrIJRU9ng5E)上买了域名
* 在git-repo里配置CNAME
  - ![](https://github.com/A-suozhang/MyPicBed/raw/master/img/20220407104202.png)
  - [官方教程](https://docs.github.com/cn/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)
- 配置SSL Secure登录（Https）
  - [一个博客](https://tzhou2018.github.io/2018/04/%E4%B8%BAGitHub-Pages%E8%87%AA%E5%AE%9A%E4%B9%89%E5%9F%9F%E5%90%8D%E5%B9%B6%E6%B7%BB%E5%8A%A0SSL-%E5%BC%80%E5%90%AFHTTPS%E5%BC%BA%E5%88%B6/)
  - 直接在 `github-repo的Settings - Pages - Enforce HTTPS`

## Other Trouble Shooting
* 网站渲染的时候出现某一个大标题右缩进
    > 大标题紧跟的行数的上一行缩进了，再空一行即可




