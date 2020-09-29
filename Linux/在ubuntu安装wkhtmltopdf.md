---
title: 在ubuntu安装wkhtmltopdf
date: 2019-12-05 11:21:14
tags: ubuntu
---
## 写在前头
这篇文章为上篇补充。文章借鉴了[Bo Andersen的文章](https://codingexplained.com/dev-ops/installing-wkhtmltopdf-on-ubuntu-14-04-and-16-04)
由于在Linux上的安装与Windows有很大不同，除了安装软件外，一个软件在linux上跑起来还得要一些依赖来支持

## 安装
安装依赖
```
apt-get install libfontconfig \
    zlib1g \
    libfreetype6 \
    libxrender1 \
    libxext6 \
    libx11-6
```
安装压缩包和解压
```
curl -L -o wkhtmltopdf.tar.xz https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz
tar -xf wkhtmltopdf.tar.xz
```
将它的二进制可执行文件移动到本地
```
mv wkhtmltox/bin/wkhtmltopdf /usr/local/bin/wkhtmltopdf
chmod +x /usr/local/bin/wkhtmltopdf
```
测试
`wkhtmltopdf https://www.baidu.com baidu.pdf`