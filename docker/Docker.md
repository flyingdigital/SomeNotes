### Docker与虚拟机的对比

|          | docker容器                                 | 传统虚拟机                         |
| -------- | ------------------------------------------ | ---------------------------------- |
| 磁盘占用 | 几十M到几百M左右                           | 几个G到几十个G左右                 |
| CPU占用  | docker引擎占用极低                         | 虚拟操作系统非常占用CPU和内存      |
| 启动速度 | (从开启容器得到项目启动)几秒               | (从开机到运行项目)几分钟           |
| 安装管理 | 安装管理方便                               | 需要专门的运维技术                 |
| 应用部署 | 第二次部署开始轻松简捷                     | 每次部署都费时费力                 |
| 耦合性   | 每个应用服务一个容器，达成隔离(操作系统级) | 多个应用服务装在一起，容易互相影响 |
| 系统依赖 | 需要相同或者相似的内核(Linux)              | 无                                 |

- docker	

  ======docker容器(mysql,redis...)

  ======docker引擎(指的是基于docker运行的软件)

  ======操作系统

  ======底层硬件

- 虚拟机

  ======mysql，redis...

  ======虚拟操作系统

  ======虚拟机

  ======操作系统

  ======底层硬件

对于虚拟机来说，一个应用想要分配内存，他必须 虚拟内存=>虚拟物理内存==>物理内存，中间需要进行虚拟化操作，非常麻烦。。简称：慢！



### docker安装

详细步骤可以移步官网(https://docs.docker.com/engine/install/centos/)

使用便捷脚本安装

```bash
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh
```

### docker核心概念

- 镜像
  - 一个镜像就相当于一个软件，它是只读的，不能修改
- 容器
  - 一个镜像run起来就是一个容器，容器是可以修改的
- 仓库     `https://hub.docker.com/`
  - docker在全世界范围内维护一个远程仓库，它用来存储全世界所有的镜像
  - 同时，在本地机器上会有一个本地仓库，在运行镜像时，优先使用本地仓库

### 配置docker镜像加速

这里使用阿里云镜像加速服务`https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors`



### docker的HelloWorld

这里涉及到两个命令：

- ```
  docker pull hello-world
  ```

- ```
  docker run hello-world
  ```

关于这两个命令的解释：

- pull命令：docker会在dockerhub上找我们要的镜像，比如hello-world。

  - 默认下载最新版本镜像

    docker pull hello-world == docker pull hello-world:latest

- run命令：这个命令首先会在本地仓库里边找需要run的镜像，如果找的到就直接启动，如果找不到就从远程仓库下载镜像，然后再run。



### docker引擎及镜像的相关操作

1. 查看docker引擎及docker帮助命令

   - `docker info`：展示docker信息

     - 出现警告

       ```
       WARNING: bridge-nf-call-iptables is disabled
       WARNING: bridge-nf-call-ip6tables is disabled
       ```

     - 解决办法及原因 

       https://developer.aliyun.com/article/278801

       https://www.qedev.com/cloud/343507.html

       - 如上操作后又出现警告(没办法外网访问)

         ```java
         WARNING: IPv4 forwarding is disabled. Networking will not work.
         ```

       - 解决方法：

         https://blog.csdn.net/weiguang1017/article/details/76212203

   - `docker version`：查看docker版本

   - `docker help`： 命令帮助信息

2. docker镜像相关命令

   - `docker iamges`： 查看所有镜像

     | REPOSITORY(仓库名) | TAG(镜像版本) | IMAGE ID(镜像id  这里只是部分id) | CREATED(创建时间) | SIZE(大小) |
     | ------------------ | ------------- | -------------------------------- | ----------------- | ---------- |
     | mysql              | 5.7           | 8a8a506ccfdc                     | 2 days ago        | 448MB      |

   - `docker pull 镜像名:Tag`: 下载镜像

   - `docker search 镜像名`: 搜索dockerhub相关镜像(没办法搜版本)

   - `docker image rm 镜像名:Tag | 镜像id`： 删除镜像

     - 等同于`docker rmi 镜像名:Tag | 镜像id`
     - `-f`选项表示强制删除(由该镜像运行的容器如果存在，未加上-f选项无法删除)

   - `docker image ls`：查看本地镜像信息 与

     - -a ： 表示查询所有所有  `docker images`默认查找所有
     - -q :    查找镜像的镜像id     可以配合删除来用

     ```bash
     $ docker image ls -q
     8a8a506ccfdc
     7faaec683238
     87a94228f133
     $ docker image ls -q mysql
     8a8a506ccfdc
     ```

     ```bash
     $ docker image rm -f $(docker image ls -q mysql)  # 表示删除所有mysql镜像
     ```

3. docker容器相关命令

   - `docker run 镜像名:Tag `：通过一个镜像运行一个容器

     - -d：	后台运行。一般运行tomcat这些容器，如果不指定-d选项，一般都会是前台启动，加上-d后台启动。

       ```bash
       $ docker run -d tomcat:8.0-jre8
       ```

       

     - -p：    由于docker容器是相互隔离的，在容器上开的端口默认是访问不到的。(比如运行一个tomcat容器，默认开启了8080端口，但通过ip:port是访问不到的)**-p选项可以使宿主机端口和容器端口进行映射！**

       ```bash
       $ docker run -d -p 8080:8080 tomcat:8.0-jre8 # 第一个8080是宿主机端口，第二个是docker容器端口
       ```

     - --name：指定容器的名字。**名字和容器id是容器的唯一标识**。

       ```bash
       $ docker run -d -p 8080:8080 --name tomcat01 tomcat:8.0-jre8
       ```

   - `docker ps`：查看正在运行的容器

     - -a：    查看所有容器，包括已经停止的容器。
     - -q:       默认查看正在运行容器的容器id

     ```bash
     $ docker ps -a -q 
     ```

   - 停止、重启容器的命令

     - `docker start 容器名字|容器id` ：可以启动一个已经停止的容器
     - `docker restart 容器名字|容器id`： 重启容器
     - `docker stop 容器名字|容器id`： 正常停止容器
     - `docker kill 容器名字|容器id`：  立刻停止容器

   - `docker rm  容器id|容器名`： 删除一个容器

     - -f： 强制删除，默认是无法删除一个正在运行的容器的

     - 可以和删除镜像一样，一次性删除多个容器

       ```bash
       $ docker rm -f $(docker ps -aq)
       ```

   - `docker logs 容器id|容器名字`：查看容器日志

     - -f：查看容器实时日志(容器处于启动状态)

     - --tail N：查看后几行日志

       ```bash
       $ docker logs --tail 5 tomcat01
       ```

   - `docker exec -it 容器id|容器名 需要执行的命令`

     - 一般写法

       ```bash
       $ docker exec -it 容器id|容器名 bash
       ```

   - 容器与宿主机之间赋值文件/目录

     - 容器->宿主机

       ```bash
       $ docker cp 容器id:文件|目录 宿主机文件|目录
       ```

       ```bash
       $ docker cp fd6e71825f29:/usr/local/tomcat/RUNNING.txt ./
       ```

     - 宿主机->容器

       ```bash
       $ docker cp 宿主机文件|目录 容器id:文件|目录 
       ```

       ```bash
       $ docker cp ./·		  fd6e71825f29:/usr/local/tomcat
       ```

   - `docker inspect 容器id|容器名`查看容器内部细节

   - 数据卷(volume)：容器可以与宿主机共享同一个目录，对目录的操作同步宿主机和容器，且容器删除不会影响数据卷。容器与容器之间可以公用数据卷

     - 使用自定义目录：使用自定义目录会使容器目录原有文件消失！

       ```bash
       $ docker run -d -p 目标端口:容器内端口 --name 自定义唯一容器名 -v 宿主机目录(绝对路径):容器内部目录 镜像名:Tag
       ```

       ```bash
       $ docker run -d -p 8080:8080 --name tomcat01 -v /home/app:/usr/local/tomcat/webapps topmcat:8.0-jre8
       ```

     - 使用默认目录：这个是把宿主机目录用一个随意的名字代替，docker会自动创建这个目录(/var/lib/docker/volune/名字/_data)，在这个目录里会把原来容器目录的文件复制过去。这样做适合一些有重要数据的容器，比如mysql。同时后面部署还是可以使用这个名字，完成数据共享操作。

       ```bash
       $ docker run -d -p 目标端口:容器内端口 --name 自定义唯一容器名 -v 宿主机目录(绝对路径):容器内部目录 镜像名:Tag
       ```

       ```bash
       $ docker run -d -p 8080:8080 --name tomcat01 -v aa:/usr/local/tomcat/webapps tomcat:8.0-jre8
       ```

     - -ro：可以使容器内只读数据卷，而容器外可以修改数据卷  

       ```bash
       $ docker run -d -p 目标端口:容器内端口 --name 自定义唯一容器名 -v 宿主机目录(绝对路径):容器内部目录:ro 镜像名:Tag
       ```

       

   - `docker commit 容器id|容器名 自定义镜像名:Tag`:打包容器，可以把一个正在运行的容器，打包成镜像

     - -m: message给打包的容器添加一段注释
     - -a:  author添加作者

     ```bash
     $ docker commit -m "this is a tomcat project" -a "flyingdigital" tomcat01 mytomcat:1.0
     ```

   - `docker save 镜像名:Tag 文件名`：把镜像打包成镜像文件

     ```bash
     $ docker sava aa:1.0 aa-1.0.tar
     ```

   - `docke load  -i 文件名`： 导入镜像文件

     ```bash
     $ docker load -i aa-1.0.tar
     ```

     

#### 小补充环节

有了这些命令，可以拿一个tomcat来练练手。

- 获取tomcat镜像: `docker pull tomcat:8.0-jre8`

- 可以同时运行多个使用同一镜像的容器，比如运行两个tomcat，这里可以很方便完成宿主机和docker容器端口的映射！

  ```bash
  $ docker run -d -p 8080:8080 --name=tomcat01 tomcat:8.0-jre8
  $ docker run -d -p 8081:8080 --name=tomcat02 tomcat:8.0-jre8
  ```

- 然后就可以通过ip访问啦。

可能遇到的问题：

- 启动tomcat容器非常的慢！
  - 原因：由于tomcat启动内部需要生成随机数，如果随机数不过就会导致tomcat启动非常慢！
  - 解决方法： https://blog.csdn.net/tiantao2012/article/details/78792046

### docker镜像分层原理

- 镜像
  - 一个镜像代表一个软件，软件包，用来打包软件运行环境和基于运行环境开发软件，它包含运行某个软件所需的所有内容，包括代码运行时所需的库，环境变量和配置文件。
- 为什么镜像这么大
  - 在官网，一个下好的tomcat听破天也就10M，而tomcat的镜像却有几百M
  - 原因：一个软件镜像不仅仅是原来的软件包，还包含软件包所需要的操作系统依赖 软件自身依赖 以及自身软件包
- 为什么docker镜像采用分层镜像原理
  - docker在设计镜像时没一个镜像都有n个镜像共同组成
  - 原理：
    - UnionFS 联合文件系统：就是一次同时加载多个文件的系统，单从外表来看，他们就象是同一个文件系统，联合加载会把各层的文件系统叠加起来，这样最终的文件系统会包含所有的底层文件和目录
    - 采用分层其实就是服用相同的镜像，前言docker中一个镜像有多个小的镜像组成，这些小的镜像有很多是可以复用的，服用就意味着节约空间！

### docker容器之间的网络通信

详细见：https://yeasy.gitbook.io/docker_practice/advanced_network

- 为什么提供网络功能

  - Docker允许通过外部访问容器或容器互联的方式来提供网络服务

- docker容器与操作系统之间的通信机制 (以下为个人理解)

  - docker容器与操作系统之间通过一个网桥来进行通信。在docker容器创建的时候，会把这个容器默认挂载到一个bridge上，同时在这个bridge和创建的容器内部创建一对veth接口，这个当数据包发送到一个接口时，另一个接口也可以收到向东的数据包，docker使用这种机制完成容器与容器，容器与操作系统之间的通信。
  - 关于网桥
    - docker默认有一个bridge网桥(docker0)。一个网桥上可以挂载多个容器，每个挂载的容器都会分配一个ip，这个ip是与容器的名字绑定的，同一个网桥上的容器相当于处于同一网段，可以完成容器间通信。
    - 一些建议：由于一个网桥上可以挂在多个容器，但这个容器的数量不宜过多，最好一个应用对应一个网桥一个网桥。

- 一些关于网络的命令

  - `docker network ls`：查看所有网桥

  - `docker network create 自定义网桥名`

    ```bash
    $ docker network create ems
    ```

  - `docker network rm 网桥名|network id`

    ```bash
    $ docker network rm ems
    ```

  - `docker inspect 网桥名|网桥id`：查看网桥信息(可以查看这个网桥上活跃的容器及其ip)

  - `docker run --network 网桥名|网桥id  容器名:Tag`：把容器挂载到某个网桥上

### 数据卷的一些操作

- `docker volume ls`: 查看数据卷
- `docker volume create 数据卷名`: 创建数据卷
- `docker volume inspect 数据卷名`： 查看数据卷信息 `docker inspect 数据卷名` 也可





### 安装一些基础服务

#### Mysql(https://hub.docker.com/_/mysql)

1. 拉取mysql镜像(我使用的是mysql:5.7)

   ```bash
   $ docker pull mysql:5.7
   ```

2. 运行MySQL，由于mysql运行需要加额外配置容器参数 `MYSQL_ROOT_PASSWORD`，所以使用到-e(environment)选项(若缺失这个参数，无法启动)

   ```bash
   $ docker run -d -p 3307:3306 --name mysql -e MYSQL_ROOT_PASSWORD=root mysql:5.7
   ```

3. 由于mysq是存储数据的，需要使用到数据卷，如果不用，mysql容器没了，那么数据也就没了。在容器中，mysql存储各种数据的地方是`/var/lib/mysql`，详细见上边连接

   ```bash
   $ docker run -d -p 3307:3306 --name mysql -e MYSQL_ROOT_PASSWORD=root -v mysqldata:/var/lib/mysql mysql:5.7
   ```

4. 配置mysql配置文件，这个配置文件可以有两种方法配置，一种是docker cp配置文件到容器里，一种是把配置文件的目录共享到宿主机。这里使用数据卷方式,容器内，mysql的配置文件都在`/etc/mysql/`目录下

   ```bash
   $ docker run -d -p 3307:3306 --name mysql -e MYSQL_ROOT_PASSWORD=root -v mysqldata:/var/lib/mysql -v mysql:/etc/mysql mysql:5.7
   ```

   这样以后直接改宿柱机配置文件就好啦

   

#### Redis



### Dockerfile (镜像构建文件)

1. Dockerfile用来干什么？

   - 它的作用只有一个，就是使用里边的指令来构建镜像

2. 为什么存在Dockerfile？

   - 虽然在dockerhub上已经提供了许多的镜像了，但是我们更想把自己的应用打包成镜像，直接运形成容器

3. 原理

   - Dockerfile每一行指令都会临时构建成一个镜像，同时后一个镜像会基于前一个镜像的基础构建，直到最后生成一个最终镜像。
   - 每次构建需要一个上下文环境，默认是当前目录，这个目录的所有文件都会提交给docker server

4. 把一个Dockerfile构建成镜像

   上下文目录里必须要是Dockerfile这个名字，不能识别的名字！

   ```bash
   # name:tag 都是自定义的 mycentos7:1.1
   # .是代表具有Dockerfile的目录  
   $ docker build -t name:tag .
   ```

   

5. Dockerfile常用指令  (https://docs.docker.com/engine/reference/builder/)

   - FROM指令

     - 基于哪个镜像进行构建新的镜像，在构建时会自动从dockerhub上拉取base镜像，这个指令必须作为Dockerfile第一个指令出现

     - 语法

       ```bash
       FROM <image>
       FROM <image>[:<tag>]	不加上tag默认版本为latest
       ```

   - RUN指令

     - RUN指令将在当前镜像之上的新层中执行任何命令并提交结果，生成的新的镜像将用于Dockerfile中的下一步

     - 语法

       ```
       RUN <command>          这个command可以是各种shell命令，也可以是脚本
       
       RUN echo hello
       RUN ["echo","hello"]	两种写法都ok
       ```

   - EXPOSE指令

     - 向外部暴露某个端口，只有暴露了端口才能在run时使用-p选项有意义

     - 语法

       ```
       EXPOSE 80		如果没有显式指定，默认暴露tcp
       EXPOSE 80/tcp 
       ```

   - WORKDIR指令

     - 修改工作目录，可使用多次。(这个目录就是使用 docker exec image:tag bash一进去的目录)，如果这个目录不存在，则自动创建。

     - 语法

       ```
       WORKDIR /data
       WORKDIR aa			aa是相对路径，相对于上一个WORKDIR，这样进去就是/data/aa
       ```

   - COPY指令和ADD指令

     - CPOY指令可以向容器内复制上下文目录中的文件

     - ADD指令不仅可以像容器内添加上下文环境的文件，更可以下载网络中的文件到容器，如果添加的上下文目录中的文件是一个压缩文件，他还会解压缩。这个解压缩只针对上下文目录的文件，从网络上下载的不会解压缩。

     - 用法

       ```
       COPY aa.txt /data/aa
       ADD xx.tar /data/aa
       ADD www.xx.com/xx.tar /data/aa
       ```

   - VOLUME指令

     - 指定一个默认的数据卷

   - CMD与ENTRYPOINT

     - 而这后面后可以接命令`ENTRYPOINT ls `
     - ENTRYPONIT指令一般写死命令
     - CMD一般用来传参(多个cmd只有最后一个可以执行，其他都被覆盖)，与ENTRYPOINT指令组合使用。

     ```
     ENTRYPONIT ["ls"]
     CMD ["/data/aa"]
     二者配合使用 但是cmd可以方便传参覆盖
     eg 将CMD覆盖为bb，一般给CMD传一个默认值，但可以修改，不过只能用json数组来传:
     	docker run image:tag "bb"
     	
     ```

     

#### 一个DLC

1. 编写一个centos镜像

   ```
   # 这个centos是没有vim命令的
   FROM centos:7
   ```

2. 给centos安装vim

   ```
   FROM centos:7
   RUN yum install -y vim
   ```

3. 开放8080端口

   ```
   FROM centos:7
   RUN yum install -y vim
   EXPOSE 8080
   ```

4. 修改WORKDIR

   ```
   FROM centos:7
   RUN yum install -y vim
   EXPOSE 8080
   WORKDIR /data
   WORKDIR aa		# 此时工作目录是/data/aa
   ```

5. 将上下文目录里的aa.txt复制到容器里

   ```
   FROM centos:7
   RUN yum install -y vim
   EXPOSE 8080
   WORKDIR /data
   WORKDIR aa		# 此时工作目录是/data/aa
   COPY aa.txt /data/aa  # aa.txt是相对路径也可以写绝对路径 当然/data/aa也可以是相对路径
   ```

6. 下载tomcat到WORKDIR

   ```
   FROM centos:7
   RUN yum install -y vim
   EXPOSE 8080
   WORKDIR /data
   WORKDIR aa		# 此时工作目录是/data/aa
   COPY aa.txt /data/aa  # aa.txt是相对路径也可以写绝对路径 当然/data/aa也可以是相对路径
   ADD https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.54/bin/apache-tomcat-9.0.54.tar.gz /data/aa
   ```

#### 跑一个简单的SpringBoot项目

Dockerfile

```
FROM openjdk:8u302-jre
WORKDIR /home/app
COPY boot.jar /home/app
EXPOSE 8080
ENTRYPONIT ["java","-jar"]
CMD ["/home/app/boot.jar"]
```



### docker-compose

1. 什么是docker-compose

   Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排(组织各个容器。它和Docker是一个公司的两种产品。

2. 用它来干啥

   Compose的定位是 **定义和运行多个Docker容器的应用**。通过Dockerfile模板文件，可以让用户很方便的定义一个属于自己的镜像。然而，在日常工作中，经常会碰到 **需要多个容器相互配合类完成某项任务**的情况。比如一个Web项目，需要数据库等等。

3. Compose中两个重要概念

   - 服务
     - 一个应用的容器
   - 项目
     - 由一组关联的应用容器组成的一个完整的业务单元，在docker-compose.yaml中定义

4. 安装(https://docs.docker.com/compose/install/)

   ```bash
   $ sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   $ sudo chmod +x /usr/local/bin/docker-compose
   ```

   实际上这样去GitHub上捞会很慢，所以建议直接从GitHub上下载下来，然后传给服务器(本人电脑Win10)

   下载后使用scp命令传输(https://blog.csdn.net/zhning12L/article/details/80422111)

#### 第一个docker-compose

需要在一个目录下创建名为`docker-compose.yaml`的文件。

编写好后直接启动，`docker-compose up`

使用docker-comopse快速配置两个个tomcat服务

```yaml
version: "3.0"		# 4.0以下都可
services:			# 可以有n多个服务
	tomcat01:		# 服务名 自定义，不重复即可
		image: tomcat:8.0-jre8	# 这个服务使用到的镜像
		ports:					# 需要向外映射的端口  是一个数组
			- 8081:8080			
			
	tomcat02:
		image: tomcat8.0-jre8
		ports:
			- 8082:8080
		
```



### docker-compose.yaml模板指令

- version

  - 指定docker-compose的版本，4.0以下都可以，必须在开头协商

- services

  - 用来定义各种服务 下面要接上各种服务的名字

- image

  - 指明服务用到的镜像

- ports(数组)

  - 指明服务对外开放的端口，端口映射最好用字符串格式

- container_name

  - 指明容器名字

- volumes(数组)

  - 指定数据卷映射，可以是自定义目录，也可以数据卷名，也需要声明

- networks(数组)

  - 指明容器所属网桥，网桥若不存在，需要声明，声明的网桥默认是 目录名_网桥名


- environment(数组)

  - 指明容器环境参数  eg(MYSQL_ROOT_PASSWORD=root)

- env_file(数组)

  - 指明容器有关环境参数的文件(可以把环境参数写在文件里，避免直接暴露重要配置)

- depends_on(数组)

  - 表明这个服务依赖于哪个服务启动，后写服务名。有趣的是，这个服务并不会等所有的依赖服务都启动好了在启动。

- build

  - 通过指定context和Dockerfile来把Dockerfile打成image

    ```
    mysql01:
    		container_name: mysql01
    		build:
    			context: /home/app
    			dockerfile: Dockerfile
    		environment:
    			- MYSQL_ROOT_PASSWORD=root
    		volumes:
    			- mysqlconf:/etc/conf
    			- mysqldata:/var/lib/mysql
    		ports:
    			- "3307:3306"
    ```

    

```yaml
version: "3.0"
services:  # 有多个所以复数
	tomcat01: 	# 服务名  自定义 唯一
		container_name: tomcat01	# 定义容器名  默认为 目录_服务名
		image: tomcat:8.0-jre8
		ports:
			- "8080:8080"
		volumes: 
			# - /home/apps:/usr/local/tomcat/webapps	# 自定义目录
			- tomcatapps:/usr/locat/tomcat/webapps   	# 数用数据卷名作为目录，卷名默认为 目录_卷名  且需要声明
		networks:
			- mywebapp
		depends_on:
			- mysql01
			
	mysql01:
		container_name: mysql01
		image: mysql:5.7
		environment:
			- MYSQL_ROOT_PASSWORD=root
		volumes:
			- mysqlconf:/etc/conf
			- mysqldata:/var/lib/mysql
		ports:
			- "3307:3306"
		networks:
			- mywebapp
# 声明数据卷		
volumes:
	tomcatapps:		
		external: 
			true		这个参数默认为false  让docker-compse自己创建  作用是使用已经存在的 数据卷

# 生命网络桥
networks:
	mywebapp:		
		
```



### docker-compose指令

1. 基本使用

```
docker-compose [-f=<arg>...] [options] [COMMAND] [ARGS]
```

2. 命令选项
   - `-f,--file FILE`指定docker-compose模板文件，默认为docker-compose.yaml
   - `-p,--project-name NAME`指定项目名称，默认为目录名
   - `--x-networking`使用docker可拔插网络后端
   - `--x-network-dirver DIRVER` 指定网络驱动 默认为bridge
   - `--verbose`输出更多调试信息
   - `-v,--version`打印版本并推出
3. 命令使用说明
   - up   
     - 该命令十分强大，他将尝试自动完成包括构建镜像，创建服务，启动服务，关联服务相关容器的一系列操作
     - 链接的服务都将会被自动启动，除非已经处于运行状态
     - 可以说，大部分的时候都可以直接通过该命令来启动一个项目
     - 默认情况：`docker-compose up`启动项目都在前台
     - `Ctrl + C`停止所有容器
     - `docker-compose up -d`后台启动并运行所有容器
   - down
     - 此命令将会停止up命令启动的容器，并移出网络
   - exec
     - 进入指定容器
   - ps
     - 格式为`docker-compose ps [option] [service]`列出项目中的所有容器
     - -q  打印容器id
   - restart
     - `docker-compose restart [option]` [service]
     - 容器项目中的服务
     - -t  指定重启前停止容器的超时，默认为10秒
   - rm
     - `docker-compose rm [option] [service]`
     - 删除所有服务容器
     - -f 强制删除
   - start
     - 启动服务
     - `docker-compose start [service]`
   - stop
     - 停止服务
     - `docker-compose stop [service]`
   - top
     - 查看容器内运行的进程
     - `docker-compose top [service]`
   - logs
     - 查看服务日志
     - `docker-compose logs [service]`

