## nginx反向代理

反向代理即 原来我要访问tomcat，而现在我访问的是nginx，由nginx再去把请求转发给tomcat，此类过程用户并无感觉。（个人理解 。。）



反向代理以tomcat为例介绍，在此之前我们得先开启8080和80端口。

```
安装tomcat
yum install tomcat

#启动tomcat  启动后可以在127.0.0.1.8080访问tomcat默认页面
service tomcat start
```



```
# tomcat 源码安装
cd /usr/local
wget https://downloads.apache.org/tomcat/tomcat-8/v8.5.58/bin/apache-tomcat-8.5.58.tar.gz
tar -xvf wget https://downloads.apache.org/tomcat/tomcat-8/v8.5.58/bin/apache-tomcat-8.5.58.tar.gz
cd apache-tomcat-8.5.58
./configure
```



### 防火墙的配置

以上配置的网站只能在localhost（本机）进行访问，要使同一局域网下的机器也可以访问，需开放80，8080端口。

```
#查看开启的端口 
firewall-cmd --list-all
#public (active)
#  target: default
#  icmp-block-inversion: no
#  interfaces: ens33
#  sources: 
#  services: ssh dhcpv6-client
#  ports: 80/tcp 8080/tcp
#  protocols: 
#  masquerade: no
#  forward-ports: 
#  source-ports: 
#  icmp-blocks: 
#  rich rules: 

# 如果没有开放以上两端口请开启
firewall-cmd --add-port=80/tcp --permanent		# 开放80端口
firewall-cmd --add-port=8080/tcp --permanent	# 开放8080端口

#重启防火墙

## 如上配置后（虚拟机） 可以在window上访问虚拟机上的tomcat  ip:8080 与默认的nginx页面 ip:80  ip为虚拟机ip ifconfig可查看
```



### nginx配置

此需要配置nginx下的nginx.conf文件中的http模块

```
    server {
        listen 80; 	#监听端口
        server_name 192.168.43.144;	  #本机ip


        location / {
                root html;
                index index.html index.htm;
                proxy_pass http://192.168.43.144:8080;	#转发地址
        }

```





配置完后重启nginx即可在window访问 须在nginx目录下完成

```
./nginx -s reload
```



遇到问题

window可以正常访问 ip:8080  无法访问 ip:80 即无法转发

解决方法

修改/etc/selinux/conf文件，修改SELINUX=disabled

如无法解决请查看nginx日志/var/log/nginx/error.log

