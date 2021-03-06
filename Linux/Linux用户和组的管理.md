---
title: Linux用户和组的管理
date: 2020-02-14 23:08:19
tags: Linux
---
## 用户
用户在计算机看来就是识别个体的一个标识，每个用户的用户名和UID(userID)是唯一的，与用户的许多信息都在**/etc/passwd**和**/etc/shadow**里。
一个用户的登录往往伴随着Authentication(认证), Authorization(授权), Audition(审计)

- 用户类型
用户主要有两种类型:	
	管理员(root)
	普通用户
		系统用户(这类用户不需要登录不需要密码，大都用来发起进程)
		登录用户

在Linux里，一个命令对文件的访问权限通常取决于发起进程的用户。例如root与普通用户发起的`cat /etc/shadow`，输出的结果就有不同。


- 用户标识: UID
	UID用十六位二进制数字表示: 0-65535
	其中,管理员: 0
	普通用户: 1-65535
		系统用户: 1-999(centos7)
		登录用户: 1000-60000(centos7)
	名称解析: 用户名< -- > UID(名称解析时用到了名称解析库/etc/passwd)


- /etc/passwd
部分内容如下
```
...
flyingdigital:x:1000:1000:flyingdigital:/home/flyingdigital:/bin/bash
gentto:x:1001:1001::/home/gentto:/bin/bash
slackware:x:1002:1002::/home/slackware:/bin/tcsh
yingyingying:x:1003:1003::/home/yingyingying:/bin/bash
gent:x:4001:4001::/home/gent:/bin/bash
...
```
以上每一行的每个属性值以`:`隔开，依次为`用户名:密码:uid:gid:对用户的一段描述(可选):用户家目录:使用的SHELL`(以上信息可以通过`man 5 passwd`查看)。在以前用户的密码记录在`/etc/passwd`,后来将密码存放在了别处(/etc/shadow),x代表密码不在这。

- /etc/shadow
这里存放的是关于用户密码的一些信息，部分内容如下
```
...
flyingdigital:$6$ld0XLVERtYphlL8a$PPrckc21T98qZq9KtkikALAeu.HNxwEw89S5wQdQzrY5Nz90D4mqF3mY2nFr66ZUx4o/ltkWpbEFfMT3Rl5/i1::0:99999:7:::
gentto:!!:18235:0:99999:7:::
slackware:!!:18235:0:99999:7:::
yingyingying:!!:18302:0:99999:7:::
gent:!!:18305:0:99999:7:::
...
```
从左往右为`用户名:密码:最后一次修改密码时间:密码使用至少多长时间后可以再改:密码使用至多时间后必须修改密码:密码警告时间段:密码禁用期:账户过期日期:保留字段`(详细可以通过`man 5 shadow`查看)。

这里的密码为加密后的密码，Linux里对密码的加密方式主要有六种
```
1. md5
2. sha1
3. sha224
4. sha256
5. sha384
6. sha512
```
## 与用户有关的命令: useradd, usermod, userdel
- 添加用户命令: useradd
   useradd [options] LOGIN
   useradd -D
   useradd -D [options]

       -u: 指定用户创建是的uid，若不指定则默认为上一个用户uid加一
       -g: 指定用户基本组id，这个组必须事先存在
       -G: 致命用户所属的附加组，多个组用逗号隔开
       -c: 指明用户注释信息 
       -d: 指明用户家目录(复制了/etc/skel)，如不指定则为/home/用户名
       -s: 指明用户所用的shell(查看有哪些shell`cat /etc/shells`)
       -r: 创建系统用户
- 修改用户属性命令: usermod
	usermod [options] LOGIN

		-u: 修改uid
		-g: 修改用户所属基本组
		-G: 修改用户附加组，原来的附加组会被覆盖
		-a: append,与-G一起使用，为用户追加附加组
		-c: 修改用户注释信息
		-d: 修改用户家目录，用户原来家目录里的内容不会被复制
		-m: 只能与-d选项一起使用，将原来的家目录移动到新家目录
		-l: 修改用户名
		-s: 修改用户默认shell
- 删除用户: userdel
	userdel [options] LOGIN
		-r: 删除用户家目录，默认删除用户时不会删除

## 组
组是用户的一个容器，用于对同一类用户进行管理(权限分配)

- 组类别
	- 一种划分
		用户主要有两种类型:	
			管理员组(root)
			普通用户组
				系统用户组(这类用户不需要登录不需要密码，大都用来发起进程)
				登录用户组
	- 另一种划分
		由于一个用户可以有多个组，所以组又可以划分为
			用户的基本组(主组)
			用户的附加组
	- 有一种划分
		私有组: 该组有且只有一个与组名相同的用户
		公共组: 组内有多个用户

- 组标识: GID
	UID用十六位二进制数字表示: 0-65535
	其中,管理员组: 0
	普通用户组: 1-65535
		系统用户组: 1-999(centos7)
		登录用户组: 1000-60000(centos7)
	名称解析: 用户组名< -- > GID(名称解析时用到了名称解析库/etc/group)

- /etc/group
```
...
flyingdigital:x:1000:flyingdigital
gentto:x:1001:
slackware:x:1002:
yingyingying:x:1003:
distro:x:5000:gent
peguin:x:5001:gent
gent:x:4001:
...
```
分别为`组名:组密码:GID:以此组为附加组的用户list`(`man 5 group`)
x代表密码在/etc/gshadow里

- /etc/gshadow
```
...
flyingdigital:!!::flyingdigital
gentto:!::
slackware:!::
yingyingying:!::
distro:!::gent
peguin:!::gent
...
```
分别为`组名:组密码:组管理员:以此组为附加组的成员list`(`man 5 gshadow`)

## 与组有关的命令: groupadd, groupmod, groupdel
- 添加组命令: groupadd
	groupadd [options] group
	常用选项
		-g: 手动指定gid，如果不指明则gid为上一个组id加一
		-r: 添加一个系统组
- 修改组属性命令: groupmod 	(mod为modify)
	groupmod [options] GROUP
		-g: 修改gid
		-n: 修改组名
- 删除组命令: groupdel
	groupdel [options] GROUP