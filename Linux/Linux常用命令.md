---
title: Linux常用命令
date: 2019-12-07 09:41:00
tags: Linux
---

## Linux文件系统
	1. 文件名称严格区分字符大小写
	2. 文件可以使用除/以外的任意字符
	3. 文件名长度不能超过255字符
	4. 以.带头的文件为隐藏文件
		.: 当前目录
		..: 当前目录的上一级目录

		eg: /etc/sysconfig
			.: /etc/sysconfig
			..: /etc

## 工作目录: working directory
## 家目录: home

## 常用命令
	pwd: printing working directory
		显示工作目录

	cd: change directory
		cd [/PATH/TO/SOMEDIR]
			cd: 切换回家目录
				注意: bash中 ~表示家目录
			cd ~:切换回自己的家目录
			cd ~USRENAME: 切换到指定用户的家目录(权限要够)
			cd -: 在上一次所在目录和当前目录来回切换

				相关的环境变量:
								$PWD: 当前工作目录(pwd命令其实访问的就是PWD这个环境变量的值)
								$OLDPWD: 上一次工作目录

	ls: list  列出指定目录下的内容
		ls [OPTION]...[FILE]...

		-a: 显示所有文件，包括隐藏文件
		-A: 显示除了.和.. 外的所有文件
		-l: --long,长格式列表，即显示文件的详细属性信息

			drwxr-xr-x.  2 flyingdigital flyingdigital 4096 Nov 27 16:20 Downloads
				drwxr-xr-x
					第一位-: 文件类型, -,d,b,c,l,s,p
				rwxr-xr-x
					前三位：文件属主的权限
					中三位：文件属组的权限
					后三位：其他用户的权限
				2： 数字表示文件被硬连接的参数
				flyingdigital: 文件的属主
				flyingdigital: 文件的数组
				4096: 文件的大小
				Nov 27 16:20: 文件最后一次被修改的时间
				Downloads: 文件名/目录名
		-h,--human-readable: 对文件的大小单位进行换算，换算后的结果可能为非精确值
		-d: 查看目录自身而非其内部文件列表
		-r: reverse,逆序显示
		-R: recursive,递归显示

	cat: concatenate 文本查看器
		cat [OPTION]... [FILE]...

	file: 查看文件内容类型
		file [FILE]...

	echo: 回显
		echo [SHORT-OPTION]... [STRING]...
			-n: 不进行换行
			-e: 让转义字符生效

			STRING可以使用一号，单引号和双引号均可使用
				单引号: 强引用 变量引用不执行替换
					echo '$SHELL'
				双引号: 弱引用 变量引用会被替换
					echo "$SHELL"

		注意: 变量引用的正规符号为 ${name} 虽然$name也成

	关机或重启命令: shutdown
		shutdown [OPTIONS...] [TIME] [WALL...]
		 OPTIONS: 
		 	-h: halt	关机
		 	-r: reboot	重启
		 	-c: cancel  取消上一步操作
		 TIME: 
		 	now			现在
		 	hh:mm		明确的几时几分
		 	+m 		 	几分钟后
		 WALL: 附带的信息(若不写则为默认)
		 	系统中有一个wall命令，相当于全局大喇叭。。

	日期相关的命令：
		Linux: 系统自动从硬件读取日期和时间信息，读取完成以后就不再跟硬件相关联
			date 系统时间
				显示日期时间: date [OPTION]... [+FORMAT]
					FORMAT: 格式符
						%F:当前的年月日
						%T:当前时间时分秒
						%Y:年
						%m:月
						%d:日
						%H:时
						%M:分
						%S:秒
						%s:显示时间戳,即1970年1月1日0时0分0秒(unix元年)距现在的秒数
					注意: +只能出现一次 如果要显示多个格式符可以用双引号括起来
						eg: date +"%Y-%m-%d %H:%M:%S"


				设定日期时间: date [-u|--utc|--universal] [MMDDhhmm[[CC]YY][.ss]]

			clock,hwclock: 硬件时钟
				显示或设定硬件时钟
					-s, --hctosys:将系统时钟设定为硬件时钟(硬件为准)
					-w, --systohc:将硬件时钟设定为系统时钟(系统为准)

			cal: 日历
				cal [options] [[[day] month] year]
