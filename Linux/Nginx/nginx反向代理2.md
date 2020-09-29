## nginx反向代理2

这里实现的是有两个tomcat，通过nginx监听某一端口实现对两个tomcat的访问

即实现监听9000端口 实现如下两个页面的访问(两个不同的tomcat)

http://192.168.43.245:9000/edu/a.html

http://192.168.43.245:9000/vod/b.html

### tomcat的准备

两个tomcat所在目录分别为

- /usr/local/tomcat8080
- /usr/local/tomcat8081

分别在这两个目录上安装tomcat（假设这两目录下已经有tomcat安装包apache-tomcat-8.5.58.tar.gz）

安装好后分别在两目录apache-tomcat-8.5.58下的webapps里创建文件夹

```
cd /usr/local/tomcat8080/apache-tomcat-8.5.58/webapps
mkdir edu && cd edu
vim a.html


cd /usr/local/tomcat8081/apache-tomcat-8.5.58/webapps
mkdir vod && cd vod
vim b.html
```



另外需要做的事情为修改/usr/local/tomcat8081/apache-tomcat-8.5.58/conf/server.xml里的内容(要将里边的端口改成与默认不同)

```
# 需要修改的标签原来为
...
<Server port="8005" shutdown="SHUTDOWN">
...
<Connextor port="8080" protocol"HTTP/1.1"
		   connectionTimeout="20000"
		   redirectPort="8443" />
...

# 将其分别求改为
# 此处的8015端口是与默认的8005端口做区分  
<Server port="8015" shutdown="SHUTDOWN">

# 此处的8081端口是与原来的8080端口做区分 其中 8080是tomcat应用的默认访问端口   现在改成8081
<Connextor port="8081" protocol"HTTP/1.1"
		   connectionTimeout="20000"
		   redirectPort="8443" />

```



配置好后分别进入其目录下的bin  然后./startup.sh  请配置好server.xml文件后做此操作，  如果提前启动，出现问题可以关闭其进程，配置好后再重新开始，方法如下

```
ps -ef | grep tomcat
# 找到pid 此处假设找到的pid 为pid
kill -9 pid
```





### nginx的配置

此处千万不要把原来监听80端口的server给搞没了  而是重新添加新的server   切记切记

```
# 进入nginx目录并修改配置文件

cd /usr/local/nginx/conf
vim nginx.conf

# 需要在http模块下 新添加一个server 内容如下

server { 
	listen 9000;					# 监听的端口
	server_name 192.168.43.245;		# 虚拟机ip
	
	# ~ 为正则匹配标志
	location ~ /edu/ {
		proxy_pass http://127.0.0.1:8080;
	}
	locattion ~ /vod/ {
		proxy_pass http://127.0.0.1:8081;
	}
}
```

配置好后重启nginx服务即可实现此反向代理



```
cd /usr/local/nginx/sbin
./nginx -s reload
```

