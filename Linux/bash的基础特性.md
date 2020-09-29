---
title: bash的基础特性
date: 2020-01-15 16:02:26
tags: Linux
---
## 写在前头 
主要记录bash的一些基础特性与一些命令：stat,touch,
								mkdir,rmdir,
								cp,mv,rm,cat,
								tac,head,tail,more,less,
								tee,tr
## 命令补全与路径补全
- 命令补全
	shell程序在接收到用户执行命令的命令的请求(输入命令后按下Enter)，分析完成之后，最左侧的字符串会被当作命令。
	命令查找机制:
		- 查找内部命令
		- 根据PATH环境变量中设定的目录，自左往右的逐个搜索目录下的文件名
	给定的打头字符串如果能唯一标识某命令程序文件，则击Tab键直接补全;不能唯一标识某命令的程序文件，再击Tab键一次，会给出列表。
- 路径补全
	在给定的起始路径下，以对应路径下的打头字符串来逐一匹配起始路径下的每个文件
		tab:
			如果能唯一标识，则直接补全;
			否则，再一次tab，给出列表;

## 目录管理类的命令
mkdir,rmdir
	- mkdir: mkdir - make directories
		mkdir [OPTION]... DIRECTORY...（这里如果不加任何options，那么只会创建最后一个目录，如果中间的目录不存在，则会报错）
			-p: 自动按需创建父类目录(这里如果中间的目录缺省则会自动创建)
			-b: verbose，显示详细过程，即先干什么后干什么
			-m MODE: 直接给定权限，一般为默认权限
	- rmdir: rmdir - remove empty directories
		注意:这是删除一个空目录，如果这个目录里有东西，则没法删掉
		rmdir [OPTION]... DIRECTORY...
			-p: 删除某目录后，如果其父目录是个空目录，则一并删除
			-v: 显示详细过程

## 命令行展开
- ~: 自动展开为用户的家目录或指定用户的家目录
- {}: 可承载一个以逗号分隔的路径列表，并能过将其展开为多个路径
	eg: /tmp/{a/{c,d},b/{c,d}} 相当于 /tmp/a /tmp/b /tmp/a/c /tmp/a/d /tmp/b/c /tmp/b/d
简单应用1:
	创建目录 /tmp/x/y  /tmp/x/z 
	`mkdir -pv /tmp/x/{y,z}`
简单应用2:
	创建目录 a_c a_d b_c b_d
	`mkdir {a,b}_{c,d}`
可以简单理解为类似于乘法分配律的一个简单应用

## 命令状态的结果
- 命令执行的状态结果:
	bash通过状态返回值来输出此结果
		成功: 0
		失败: 1~255
	命令执行完后，其状态返回值保存于bash的特殊变量**$?**中(可用`echo $?`查看)
- 命令返回值
	根据命令及其功能不同，结果各不相同
- 引用命令的执行结果
	$(COMMAND)或\`\`
	假如需要以当前年月来创建目录`mkdir $(date +%Y-%m-%d)`

## 引用
强引用:**''**
弱引用:**""**
命令引用:**$()**或**\`\`**

## 快捷键
Ctrl+a: 跳转至命令行行首
Ctrl+e: 跳转至命令行行尾

Ctrl+u: 删除行首至光标所在处之间的所有字符
Ctrl+k: 删除光标所在处到行尾之间的所有字符

Ctrl+l: 清屏,相当于clear

Ctrl+Shift+c: 复制
Ctrl+Shift+v: 粘贴

Esc+.: 引用上一个命令的最后一个参数
## 文件查看类命令
cat,tac,head,tail,more,less
	分屏查看命令more和less:
		more:
			more FILE
			特点: 翻屏至文件尾部后自动退出,实际上是对less的引用
		less:
			less FILE
		head: 查看文件的前n行(默认十行)		
			head [options] FILE
			-n #(#为要查看的行数)
			-#
		tail: 查看文件的后n行(默认十行)
			-n #
			-#
			-f: 查看文件尾部内容结束之后不退出，跟随显示新增的行，这一特性常用于人工查看日志

## stat
stat - display file or file system status
stat FILE
文件： 两类数据
	元数据: metadata
	数据:data
时间戳:
	Access: 2020-01-15 14:07:41.120815464 +0800
	Modify: 2020-01-15 17:02:31.911844267 +0800
	Change: 2020-01-15 17:02:31.911844267 +0800

## touch
touch - change file timestamps
touch [OPTION]... FILE...
常用选项:
	-c: 指定的文件路径不存在时不予创建
	-a: 仅修改access time
	-m: 仅修改modify time 
	-t STMP
	将时间出修改为指定时间
		use [[CC]YY]MMDDhhmm[.ss] instead of current time

## 文件管理工具: cp,mv,rm
- cp: copy
	源文件(SOURCE)， 目标文件(DEST)

	单源复制: cp [OPTION]... [-T] SOURCE DEST
		如果DEST不存在，则事先创建此文件， 并复制源文件数据到流到DEST中
		如果DEST存在， 
			如果DEST是非目录文件: 则覆盖目标文件
			如果DEST是目录文件: 则现在DEST目录下创建一个与源文件同名的文件，并复制其数据流

	多元复制: cp [OPTION]... SOURCE... DIRECTORY
       		 cp [OPTION]... -t DIRECTORY SOURCE...
       		如果DEST不存在: 错误
       		如果DEST存在: 
       			如果DEST是非目录文件: 错误
       			如果DEST是目录文件: 分别复制每个文件至目标目录中，并保持原名

    常用选项:
    	-i: interactive 交互式复制，即覆盖之前提醒用户确认
    	-f: 强制覆盖目标文件
    	-r: 递归复制目录
    	-d: 如果没有这个选项复制符号链接时复制的是链接的源文件的数据流但名字使用的是链接文件的名字。加上这个选项那么我们复制的是链接文件本身的数据而不再复制源文件数据。
    	-a: 复制文件的所有属性同原来一样。
    	--preserve=
    		mode: 权限
    		ownership: 属主和属组
    		timestamp: 时间戳
    		context: 安全标签
    		xattr: 扩展属性
    		links: 符号链接
    		all: 上述所有属性
- mv: move
 	mv [OPTION]... [-T] SOURCE DEST
    mv [OPTION]... SOURCE... DIRECTORY
    mv [OPTION]... -t DIRECTORY SOURCE...

    常用选项: 
    	-i: 交互式
    	-f: force

-rm: remove
	rm [OPTION]... FILE...
	常用选项: 
		-i: interactive
		-f: force
		-r: 递归删除，删除目录是使用
	该命令慎用，危险操作，建议另建一个目录，专门存储不需要的文件

## globbing: 文件名通配
匹配模式: 元字符
	\*: 匹配任意长度任意字符
		pa\*(以pa开头的文件)，\*pa(以pa结尾的文件)
	？: 匹配任意单个字符
		pa？(以pa开头后边跟任意字符的文件)
	[]: 匹配指定范围内的任意单个字符
		几种特殊的格式:
			[a-z],[A-Z]:	匹配单个字母(此处不分大小写)
			[0-9]:		匹配单个数字
			[a-z0-9]:	匹配单个字母或数字
			[[:upper:]]:	匹配单个大写字母
			[[:lower:]]:	匹配单个小写字母
			[[:digit:]]:	匹配单个数字
			[[:alpha:]]:	匹配单个字母(包括大写和小写)
			[[:alnum:]],[[:alpha:]\[:digit:]]: 匹配单个字母或数字
			[[:punct:]]:	匹配单个标点符号
			[[:space:]]:	匹配单个空白字符
	[^]: 匹配指定范围外的单个字符(取反)
		eg: \[^[:upper:]]: 匹配除大写字母以外的单个字符

## IO重定向及管道
- 程序: 指令+数据
	IO为计算机组成部分，计算机有IO，程序也有IO
	可用于输入的设备: 文件
		键盘设备，文件系统上的常规文件，网卡等
	可用于输出的设备: 文件
		显示器，文件系统上的常规文件，网卡等
	程序的数据流有三种:
		输入的数据流: <-- 标准输入(stdin), 键盘
		输出的数据流: --> 标准输出(stdout), 显示器
		错误的输出流: --> 错误输出(stderr), 显示器
	fd: file descriptor, 文件描述符
		标准输入: 0
		标准输出: 1
		错误输出: 2
- IO重定向: 
	输出重定向: > 
		特性: 覆盖输出
	输出重定向: >> 
		特性: 追加输出
	# set  -C
		禁止覆盖输出重定向至已存在的文件
		此时可以强制覆盖输出
	# set +C
		关闭上述特性
	错误输出重定向: 2> 2>> 
	合并正常输出流与错误输出流
		(1)$>, $>>
		(2)COMMAND > /path/to/somefile 2>&1
		   COMMAND >> /path/to/somefile 2>&1
	输入重定向: < 
	tr命令
		 tr [OPTION]... SET1 [SET2]
		 	把输入的数据当中的字符，凡是在SET1定义范围内出现的，通通对位转换为SET2出现的字符
		 用法一:	
		 	tr SET1 SET2 < /PATH/FROM/SOMEFILE
		 用法二:
		 	tr -d SET1 < /PATH/FROM/SOMEFILE
		 注：不会修改源文件
	Here Document: <<
		cat << EOF
		cat > /path/to/somefile << EOF

- 管道: 连接程序，是先将前一个命令的输出直接定向到后一个程序当作输入流
	COMMAND1 | COMMAND2 | COMMAND3

	tee命令: 
		COMMAND | tee /path/to/SOMEFILE
		把能把输入流保存到文件里，并输出数据流
		

