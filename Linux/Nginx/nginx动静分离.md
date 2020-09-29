## nginx动静分离

动静分离就是把访问静态资源的请求路由到一台静态资源服务器，而把动态请求路由太动态资源服务器。

静态资源： html，css，图片，视频等等

动态资源： 要访问数据库



nginx.conf配置如下

```
# 在http下server模块中添加location
# 下边所谓的www为自己创建的目录(真实路径为/data/wwww)，我们可以在www目录下创建文件以访问
...
location /www/ {
	root /data/
}
...

# 如创建/data/www/a.html
# ip/www/a.html  就能访问改静态资源

# 动态资源在tomcat下
```



​	