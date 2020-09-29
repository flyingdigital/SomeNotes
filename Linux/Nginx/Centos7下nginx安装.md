## Centos7 下nginx的安装

## 方法一: 源码安装

源码安装需要下载相应依赖的压缩包，解压编译后安装。

- 依赖的安装

  ```yum install gcc -c++ 
  yum install -y make pcre-devel zlib zlib-devel gcc-c++ libtool openssl  openssl-devel 
  ```
  
- 下载nginx包

  ```
  #安装包在/usr/local下
  cd /usr/local
  wget -c https://nginx.org/download/nginx-1.10.1.tar.gz
  #此安装安装的压缩包路径为当前路径
  ```

- 解压编译

  ```
  tar -xzvf nginx-1.10.1.tar.gz
  cd nginx-1.10.1.tar.gz
  ./configure
  make && make install
  
  
  # 之后应在/usr/local/sbin/nginx 找到
  ```



### 方法二：yum安装

```
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

sudo yum install -y nginx

sudo systemctl start nginx.service


# 此法安装nginx应在 /etc/nginx 下
```

