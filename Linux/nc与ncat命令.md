---
title: nc与ncat命令
date: 2020-04-13 21:27:36
tags: Linux
---

主要介绍*nc*与*ncat*命令在Kali Linux上的简单使用，包括文本传输，文件传输，端口扫描，远程克隆硬盘，远程控制以及对以上的加密。

## nc
### 文本传输
即两台主机可以实时传输文本，使用如下命令即可
主机A: `nc -lp 333` (`-l` 为listen监听之意  `-p` 为port端口之意 333为监听的端口号)
主机B: `nc -nv 192.168.0.111 333` (`-n`为直接收ip地址 `-v`为verbose ip可通过`ifconfig`命令查看)
另一种实现方式
主机A: `nc -nv 192.168.0.112 333`
主机B: `nc -lp 333`
两种方法的区别是打开与连接端口的主机不同，也可以将命令的标准输出通过管道进行传输

### 文件传输
- 普通文件
主机A: `nc -nv 192.168.0.112 333 < hello`
主机B: `nc -lp 333 > otherHello`		(此处*hello*与*otherHello*为文件名，其中hello是传输的文件，otherHello是接收后保存的文件 `<`可以点击[此处](https://flyingdigital.github.io/2020/01/15/bash%E7%9A%84%E5%9F%BA%E7%A1%80%E7%89%B9%E6%80%A7/#more)查看使用)
另一种实现方式，文件也可已从监听端输入
主机A: `nc -lp 333 < hello`
主机B: `nc -nv 192.168.0.111 333 > nhello`

- 目录
传输目录时需进行打包解压操作
主机A: `tar -cv -f - Music/ | nc -lp 333 -q 1`
主机B: `nc -nv 192.168.0.111 333 | tar -xv -f -`		(`-q`表示传输完成后多少秒自动退出)

### 端口扫描

`nc -nvz 0.0.0.0 1-65535` 	(默认对tcp端口进行扫描)
`nc -nvzu 0.0.0.0 1-1024`	(对udp端口进行扫描)


### 远程克隆硬盘
主机A: `nc -lp 333 | dd of=/dev/sda`
主机B: `dd -if=/dev/sda | nc -nc 192.168.0.111 333`

### 远程控制
主机A: `nc -lp 333 -c bash`
主机B: `nc 192.168.0.111 333`


## ncat
ncat可以作为nc的一个升级版，它可以进行加密传输与身份验证

主机A: `ncat -c bash --allow 192.168.0.112 -nvl 333 --ssl`
主机B: `ncat -nv 192.168.0.111 333 --ssl`