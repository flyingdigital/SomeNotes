---
title: 使用hexo+nexT+github搭建个人博客
date: 2019-08-04 21:37:25
tags:
	- hexo
categories:
	- 总结
---


## 软件安装
1.[node.js](https://nodejs.org/en/)
2.[git](https://git-scm.com/downloads)
3.hexo
> 安装好前两个后任意地方鼠标右键打开Git Bash，输入`$ npm install hexo-cli -g`来安装hexo，但由于国内网络原因我们可能安装不上，这里使用淘宝的npm镜像`$ npm install -g cnpm --registry=https://registry.npm.taobao.org`，再用它在安装hexo `$ cnpm install -g hexo-cli`。(以上操作均在Git Bash完成)
## 使用到的官方文档
1.[hexo](https://hexo.io/docs/)
2.[next](http://theme-next.iissnan.com/getting-started.html)
## 常用到的命令
- `hexo g` g为generate的缩写，意为生成静态文件
- `hexo s` s为server的缩写，意为启动服务
- `hexo clean`  官方解释为**清除缓存文件 (db.json) 和已生成的静态文件 (public)。在某些情况（尤其是更换主题后），如果发现您对站点的更改无论如何也不生效，您可能需要运行该命令。**
- `hexo d` d为deploy部署网站之意，即将本地的博客部署到仓库，在后边会用到再补充。
## 初始化博客
任意位置新建并进入一个文件夹，右键打开Git Bash`hexo init`初始化博客
## 下载next主题
在你博客的根目录下打开Git Bash,输入`git clone https://github.com/iissnan/hexo-theme-next themes/next`。耐心等待即可。
## 对主题的配置
- **使用next主题，并将语言改为中文**
进入博客的根文件，打	开_config.yml	文件，找到Site里的language属性，在后面加上zh-Hans(简体中文)这里要注意langeuage：后面要有一个空格。接着一直滑到底部，看到Extensions属性里有一个**theme**，将后面的**landscape**更改为**next**，着用用Git Bash点击自己的博客文件，输入`hexo g`，再输入`hexo s`进入网页即可观看效果。![hexo1使用next主题并将语言改为中文的网页截图.png](https://i.loli.net/2019/08/04/lFQYt6i4HJjT1Nz.png)
- **增加并配置标签页与分类页**
首先在博客的根目录里边进入theme/next，找到_config.yml,打开后找到menu这里会发现**tags**跟**categories**被注释了，将注释删去后运行，我们会得到这样的页面![hexo2搜索menu并启用分类页与标签页.png](https://i.loli.net/2019/08/04/APdjf9XIhH7WuOq.png)![hexo3启用分类页与标签页后的网页.png](https://i.loli.net/2019/08/04/tuqzxBASZdsUjE4.png)随后点击标签，发现页面错误，我们接下来配置这个页面。![hexo4点击tags后的网页.png](https://i.loli.net/2019/08/04/dtaWSMRXOhuJcUP.png)我们先进入**source**文件夹里，只有一个**_posts**,接着我们在Git Bash里输入`hexo n page tags`会发**source**这个文件夹里会多一个**tags**文件夹，进入里边的**index**修改如下![hexo5tags里的index.png](https://i.loli.net/2019/08/04/l1sHq2LUYSbc5Bz.png)这里多加入了一个**type** ，加于不加效果如下:(先是加的，后是不加)![hexo6加type.png](https://i.loli.net/2019/08/04/b2AvfkCTPlyXKc3.png)![hexo7不加type.png](https://i.loli.net/2019/08/04/ZEhNlzYKq4m3p21.png)当然如果我加入**tpye**但是后边的值是**categories**效果如下![hexo8tpye的值为categories.png](https://i.loli.net/2019/08/04/zemwyVTSlCB35Kj.png)接下来分类页面也是如此。
- **更改主题**
在**theme/next**下找到**_config.yml**找到**scheme**，这里有提供四个主题，可以根据喜欢来更改。这里我选的是最后一个。效果如下：![hexo9更换主题.png](https://i.loli.net/2019/08/04/lzJI7EkiLRwtBUC.png)
- **配置个人信息(更改自己的头像，姓名与title)** 
打开博客下的配置文件**_config.yml**,分别修改**title**与author，再搜索**avatar**，可以使用图片的url，或者是图片的路径(图片要在**source**里)![hexo10配置个人信息.png](https://i.loli.net/2019/08/04/Cv2dF6K8mIpl3Pn.png)
- **配置交友链接**
打开**next**里的配置文件，找到**social**，分别启用**Github**，**E-mail**，也可以自己加上微博或者其他的，配置与效果如下。(这里我加了自己的网易云音乐)![hexo11配置social.png](https://i.loli.net/2019/08/04/b1jvpI9JDW4hEUi.png)![hexo12配置完交由由连接的页面.png](https://i.loli.net/2019/08/04/wZYXlvCTWcM7dLj.png)
- **让自己的头像变为圆形，添加旋转功能**
进入**themes\next\source\css_common\components\sidebar/sidebar-author.styl**修改如下：![hexo13让头像变为圆形可转.png](https://i.loli.net/2019/08/04/oDw1JzxPF3sTKpZ.png)第一个是让头像变为圆形，注释的是让头像旋转。
- **设置阅读全文**
这里有两种方法一种是修改主题里的配置文件![hexo14设置阅读全文.png](https://i.loli.net/2019/08/04/7anpVPg2ocY9QTw.png)另一个是在文章里加入`<!-- more -->`代码
- **添加动态背景**
同样也是在主题的配置文件里搜索**canvas**，会有四个动态背景供选择，我选择none。![hexo15背景动画.png](https://i.loli.net/2019/08/04/ijf7eWEQa2VZ64L.png)
- **添加建站时间**
在主题的配置文件里加入**since**字段*eg:since:2010-2011*这个时间会在页面最下面显示。![hexo16建站时间显示.png](https://i.loli.net/2019/08/04/rAw2LUnqGJSDWNs.png)
- **设置链接颜色**
在**\themes\next\source\css_common\components\post\post.styl**里设置如下可以设置自己喜欢的颜色([十六进制颜色表](https://blog.csdn.net/andyguan01_2/article/details/86623335))![hexo17链接样式.png](https://i.loli.net/2019/08/04/9FSiWhN6jEAZcdw.png)
- **增加搜索服务**
进入[next官方文档](http://theme-next.iissnan.com/getting-started.html)添加一个**local search**的搜索服务(也可以用别的，但是别的要注册啥的麻烦，所以我就用了它)![hexo18搜索服务.png](https://i.loli.net/2019/08/04/92gIpG1POyHli4x.png)
- **添加评论系统**
进入[第三方服务的官方文档](http://theme-next.iissnan.com/third-party-services.html)可以使用**来必力**的评论系统，但因为国内网络原因，加载非常慢被广大网友吐槽。我本来想用**valine**的评论系统，但注册后发现需要实名认证，果断放弃。所以我的博客没有评论系统。
- **增加统计服务**
在主题文件配置如下(这里用的是不蒜子统计)![hexo19统计服务.png](https://i.loli.net/2019/08/04/8OAyX57DFWpVlSR.png)虽然我觉得有些不对劲，但还是先用着吧！
用了会发现不蒜子无法正常计数(访问几次没啥效果)，发现官方给的说法是![hexo26不蒜子更新.png](https://i.loli.net/2019/08/05/dPxM4yhpOtj16Nc.png)解决方法：进入**\themes\next\layout\_third-party\analytics\busuanzi-counter.swig**将`  <script async src="https://busuanzi/busuanzi/2.3/busuanzi.pure.mini.js"></script>`改为`  <script async src="https://busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>`即可
- **设置加载效果**
在主题的配置文件搜索**pace**字段配置如下![hexo21设置加载效果.png](https://i.loli.net/2019/08/04/J4YeOM8yEdAcmTj.png)有多种选择可以尝试。
- **字数统计与阅读时长**
在博客根目录输入如下命令`$ npm install hexo-wordcount --save`然后在主题的配置文件搜索**post_wordcount**关键字，修改如下![hexo22字数同体积与阅读时长.png](https://i.loli.net/2019/08/04/zkdRMmOIQwHf8iE.png)
-------
> 至此本地博客主题配置完成，下面将它部署到Github即可。
## 将本地博客部署到Github
- 新建仓库
注册/登录Github，新建仓库(这里要注意仓库名的形式一定要为**yourname.github.io**，**yourname**是你的Github的名字)![hexo23github仓库创建.png](https://i.loli.net/2019/08/05/4MLCwp6tgc8xnYv.png)
- 接着我们来为Git配置Github的账号邮箱以及密钥
`$ git config --global user.name "flyingdigital"// 你的github用户名，非昵称`
`$ git config --global user.email  "zhaoqian123@outlook.com"// 填写你的github注册邮箱`(输入完这两个后是啥都没有的)
接着我们在电脑上生成密钥并将它添加到Github上边，`$ ssh-keygen -t rsa -C "你的github注册邮件地址"`用这个来实现本地与远程仓库的连接。第一次的时候密钥会直接显示，复制下来即可。如果已经生成过，可以按照它的提示在自己电脑上找到（这里要说明我们要的密钥是在下图的**id_rsa.pub**,而不是**id_rsa**）![hexo24密钥所在文件夹.png](https://i.loli.net/2019/08/05/GoV6JZ1K3jQhC5U.png)同样，这里也是将它复制下来。在自己Github的settings里找到**SSh and GPG keys**项，在**SSH keys**里加入刚刚复制的密钥(title可以随意起一个)在这里可以先复制好自己的仓库的地址
- 在hexo里配置
进入hexo的配置文件，找到depoly字段，配置如下![hexo25deploy配置.png](https://i.loli.net/2019/08/05/xFkvARwiDdqjTtE.png)
- **远程部署**
在博客根目录打开Git Bash输入`hexo g`,`hexo d`这里第二个命令可能会出现问题，大概就是说找不到git，这里我们安装一下插件就可以解决`$ npm install hexo-deployer-git --save`,安装插件后在输入`hexo d`即可部署完成。
## 上传文章
在博客的根目录里打开GitBash，输入`hexo n 你文章的名字`即可创建新的文章，这个在**source/_posts**里可以看到

-------

>至此，博客搭建完毕。
>主题除了**next**也可以使用其他的，也可在hexo的官方文档里的[主题](https://hexo.io/themes/)里选择。