## nginx负载均衡

1. 实现效果  浏览器地址栏输入http://192.168.43.245/edu/a.html，实现负载均衡，平均分配到8080与8081端口中。

2. 准备工作

   - 两台tomcat服务器，端口分别为8080与8081
   - 两台tomcat服务器里的webapps目录下都有同样文件名的文件(eg:a.html)

3. 在nginx配置文件中的配置

   ```
   # 在http模块中加入负载均衡的配置
   # myserver为自己定义的名字
   upstream myserver {
   	server 192.168.45.243:8080;
   	server 192.168.45.243:8081;
   }
   
   # 修改http模块中的server
   server {
   	listen 80;
   	server_name 192.168.45.243;
   	
   	location / {
   		proxy_pass http://myserver;
   	}
   }
   ```

4. nginx分配服务器策略

   - 轮询(默认)： 每个请求按时间顺序之一分配到不同的后端服务器，如果服务器down掉(出问题)，就直接删除。

   - 权重：给每个后端服务器分配权重(权重默认为1)，权重越高，分配的客户端越多。权重用weight表示,分配如下

     ```
     # upstream里的权重
     # 此配置代表8081端口分配的客户端要比8080多一倍
     upstream myserver{
     	server 192.168.43.245:8080 weight=5;
     	server 192.168.43.245:8081 weight=10;
     }
     ```

   - ip_hash：这种配置可以记住客户端第一次访问tomcat服务器的ip，即某ip第一次访问如果实际访问的是8080端口，那么以后这个ip访问的都是8080端口，若第一次访问的是8081端口，那后边这个ip访问的都是8081端口。

     ```
     # upstream里的ip_hash
     
     upstream myserver{
     	ip_hash;
     	server 192.168.43.245:8080;
     	server 192.168.43.245:8081;
     }
     ```

   - fair： 这种配置是哪个服务器访问的速度快就访问谁。

     ```
     # upstream里的ip_hash
     
     upstream myserver{
     	server 192.168.43.245:8080;
     	server 192.168.43.245:8081;
     	fair;
     }
     ```

     