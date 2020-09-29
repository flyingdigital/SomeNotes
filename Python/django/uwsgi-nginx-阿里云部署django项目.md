---
title: uwsgi+nginx+阿里云部署django项目
date: 2019-11-02 09:17:34
tags: django
---

## 写在前头
我用到的配置：
python版本：3.6
服务器：阿里云Ubuntu16.04

这篇文章只涉及部署内容，不涉及具体**django代码**，且我在部署时使用的时创建django项目时使用的是默认的**SQLite数据库**，使用MySQL,MongoDB或者其他数据的大佬们请自行下载配置。由于在我部署时python3.7有些不稳定所以改用python3.6版本(python3.8如今也出来了)。

由于不涉及django代码，所以请大家将自己要部署的django项目上传到GitHub上(Gitee)，以方便在部署时直接克隆项目。

## 部署前的python环境配置
由于Linux一般自带python2.x版本，而咱们现在用的python版本为3.x(大家可以通过`python -V`来查看python版本)，所以第一步咱们要配置python环境。另外，在部署时咱们要用到虚拟环境(virtualenv)和相关库(django等),所以在虚拟环境里，咱们也得把这配置好。下边实在服务器上的操作：

- 安装python3.6
`apt-get install software-properties-common`
`add-apt-repository ppa:jonathonf/python-3.6`
`apt-get update`
`apt-get install python3.6`

以上步骤除了在安装时需要按**y**或者**Enter**确定安装外，最容易出现的错误是出现一个**Unable**啥的(即不能安装或安装失败),这个时候执行一下`apt-get update`来更新一下系统所用的源(个人理解为软件包),再执行其他命令，就完事了。

- 创建软连接
`cd /usr/bin`
`rm python`
`ln -s python3.6 python`
`rm python3`
`ln -s python3.6 python3`

这样做的目的时使**python**和**python3**指向**python3.6**(因为python默认指向python2.x)。个人理解就是你桌面上有英雄联盟的一个快捷方式(链接文件)，它指向英雄联盟的一个可执行文件，即点击这个桌面图标或者它实际指向的文件是同一个效果。这里咱们干的就是这个事。

- 安装pip3.6
`apt-get install python3-pip`
`pip3 install --upgrade pip`

这里可能会出现不能安装的情况，且使用`apt-get update`似乎也于事无补，但可以通过重新登录的方式解决(退了重进)。还有就是大家的pip可能报错，解决的思路跟上边一样，我把pip给他删了，再让它重新指向pip3.6(同样是在 **/usr/bin**目录里操作)
`cd /usr/bin`
`rm pip`
`ln -s pip3.6 pip`
`rm pip3`
`ln -s pip3.6 pip3`

- 安装虚拟环境
`pip install virtualenv`

想必大家肯定熟悉这个语句。

- 创建虚拟环境
这里我所在的目录是 **/home**
`virtualenv WeekSummaryApp_env`
`source WeekSummaryApp_env/bin/activate`

这里虚拟环境的名我是用**自己项目的名称 + _env**方便操作区分。上边第一条语句为创建虚拟环境，创建后该目录里是有东西的(不必纠结是啥)，接着我们启动虚拟环境(启动后会发现在命令行前边多了一个**(WeekSummaryApp_env)**)

- 安装相关库及Git
注意：一下操作均在虚拟环境里完成且根据个人所需安装相关库时有所不同。
`pip install django`
`pip install djangorestframework`
...
`apt-get install git`（瞬间打脸，这个也可以不用在虚拟环境里装，但咱就图个快活方便）

- 克隆项目
`git clone 项目地址`

这个也要在虚拟环境下完成。

## 使用uwsgi + nginx部署
这里有兴趣的童鞋可以去看[官方文档](https://uwsgi.readthedocs.io/en/latest/tutorials/Django_and_nginx.html),这里主要介绍操作。

- 安装uwsgi
`apt-get install libpython3.6-dev`
`pip3 install uwsgi`

这里值得说的时如果没有**libpython3.6**，下一步命令是无法成功执行的。

- 配置uwsgi
`cd /homne`
`mkdir WeekSummaryApp_uwsgi`
`cd WeekSummaryApp_uwsgi`
`vim WeekSummaryApp.ini`
        
如果在安装uwsgi时出现`ModuleNotFoundError: No module named 'gdbm'`啥的可以执行`apt-get install python3-gdbm`然后再安装uwsgi即可


WeekSummaryApp.ini里的内容
```
[uwsgi]
chdir = /home/WeekSummaryApp
home = /home/WeekSummaryApp_env
module = WeekSummaryApp.wsgi:application

master = True
processes = 4
harakiri = 60
max-requests = 5000

socket = 127.0.0.1:8001
uid = 1000
gid = 2000

pidfile = /home/WeekSummaryApp_uwsgi/master.pid
daemonize = /home/WeekSummaryApp_uwsgi/WeekSummaryApp.log
vacuum = True
```
- 启动uwsgi
`uwsgi --ini /home/WeekSummaryApp_uwsgi/WeekSummaryApp.ini`

启动完成后会发现在/home/WeekSummaryApp_uwsgi里多了两文件。
我们可以通过`ps -aux | grep uwsgi`来瞅瞅它到底启动没有，如果执行该命令有多行都有uwsgi则启动成功，要是只有一行则可能是代码敲错了(亲身体会)

- 安装nginx
`apt-get install nginx`

- nginx配置文件
`cd /etc/nginx/sites-available`
`rm default` 没有可忽略这一步
`vim WeekSummaryApp.conf`   编写配置文件  WeekSummaryApp是文件名

WeekSummaryApp.conf里的内容
```
server {
        listen 80;
        server_name WeekSummaryApp;
        charset utf-8;

        client_max_body_size 75M;

        location /static {
                alias /home/WeekSummaryApp/static;
        }

        location /media {
                alias /home/WeekSummaryApp/media;
        }

        location / {
                uwsgi_pass 127.0.0.1:8001;
                include /etc/nginx/uwsgi_params;
        }
}
```
- 在sites-enabled创建软连接
`ln -s /etc/nginx/sites-available/WeekSummaryApp.conf /etc/nginx/sites-enabled/WeekSummaryApp.conf`

这是会发现在**/etc/nginx/sites-enabled/**里多了一个**WeekSummaryApp.conf**

- 启动nginx服务
`nginx -t`
`service nginx restart`

启动完成后就能在公网看到自己的django项目

## 最后的说明

由于这是基于我自己的一个项目来部署的(项目名为**WeekSummaryApp**)，所以各位部署自己的项目时将它改成自己项目名就好了。

由于我的这个项目使用的是**SQLite数据库**(这个数据库是只读数据库，即readonly)，在运行时会报错。解决方法如下：

`cd /home/WeekSummaryApp`
`chmod 777 db.sqlite3`
`cd ..`
`chmod 777 *`

改变它的权限后就能正常使用了。
