---
title: mongodb的基本操作
date: 2019-12-20 13:41:53
tags: mongo
categories: database
---
## 写在前头 
文章主要介绍mongodb下载后的操作，具体为**安装**，**添加用户**，**增删改查**。最后将与python爬虫一起搞事情。
mongodb可以去他的[官网下载](https://www.mongodb.com/download-center/community)(我正在使用的是**Windows下的4.2版本**，如果您使用的是Windows系统，建议下载mis包而不是zip)

## 安装
首先跟着安装程序一顿next，安装好数据库后的配置可以参考[官方文档](https://docs.mongodb.com/guides/server/install/),这里简要说说别的方法。
 - 打开cmd，进入mongodb安装盘输入命令`md \data\db`，没有这一步mongodb没办法开启服务。

 - 在mongodb的安装目录下(eg:C:\Program Files\MongoDB\Server\4.2)新建**三个文件夹**,分别为**data**(data里面存放的是 启动了配置之后的一些信息，自动生成的，不用管。不过这里面的东西，对后面启动失败的时候有用处)，**etc**(配置文件)，**logs**(log里面存放的是 mongodb服务启动时的日志)。接下来分别在etc和logs里创建mongodb.conf和mongodb.log文件，mongodb.conf的内容大致如下
 ```dbpath=C:\Program Files\MongoDB\Server\4.2\data	#数据库路径
logpath=C:\Program Files\MongoDB\Server\4.2\logs\mongodb.log	#日志输出路径\
logappend=true	#错误日志采用追加模式，配置这个选项后mongodb的日志会追加到现有的日志文件，而不是重新创建一个新文件
journal=true	#启用日志文件，默认启用
auth=true		#开启mongo的安全认证机制
quiet=true	
port=27017
```
	mongodb.log只需要有这个文件即可。

- 在cmd里进入mongodb安装目录的bin目录下，启动该配置`mongod --config "C:\Program Files\MongoDB\Server\4.2\etc\mongodb.conf"`

完事以后就配置完事了，开始进入操作。

## 启动mongodb服务
打开cmd 输入命令`mongod`
这个终端不要关，再打开一个终端，输入命令`mongo`
就能进入mongodb开始操作了。

## 添加用户
查看现有的数据库`show databases`或者`show dbs`
添加用户我们得先进入**admin**这个数据库里`use admin`，然后进行添加用户操作
添加第一个用户`db.createUser({user:"用户名",pwd:"密码",roles:["root"]})`。这里加入的第一个用户期望是一个有root权限的用户，它可以对其他用户进行再操作。
添加一个普通用户`db.createUser({user:"用户名",pwd:"密码",roles:["read"]})`

roles为用户权限，普通用户只有**read**和**readWrite**两种权限

查看所有用户`show users`
用户登录`db.auth("用户名","密码")`
删除用户`db.dropUser("用户名")`


这些操作要在admin这个数据库里完成。还需要注意的是root用户自己可以把自己给干死，干死以后很麻烦，由于普通用户没有删除和创建用户的权限，干不了啥事，解决起来很麻烦。

## 增删改查

### 对数据库的操作
在mongodb里新建数据库比较简单，直接`use 数据库名`，如果没有在这个数据库里加入了数据，则可以自动创建数据库。

`db.dropDatabase()`表示删除当前数据库

### 对集合的集合(collection)操作

- 增加一个新的集合`db.createCollection("集合名字")`
- 删除一个集合`db.集合名字.drop()`

### 对集合数据的操作

#### 插入(增加)
在一个集合里插入数据(键值对）`db.集合名字.insert({键:"值"})`可以为多个键值对

#### 删除
- 删除集合里的所有符合条件数据 `db.集合名字.remove({键："值"})`
- 如果只想要删除符合条件的第一条数据，`db.集合名字.remove({键："值"},{justOne:true})`
- 删除一个集合所有数据 `db.集合名字.remove({键："值"},{justOne:true})`

#### 修改
- 对集合匹配到的第一个符合条件数据的修改`db.集合名字.update({键:"值"},{$set:{键:"另一个值"}})`
- 对匹配到所有符合条件的数据进行删除`db.集合名字.update({"键":"值"},{$set:{键:"另一个值"}},{multi:true})`
- 删除某一个键`db.集合名字.update({键:"值"},{$unset:{"键":""}})`。第二个参数不用指定值。

#### 查询
- 查找一个集合里的所有数据`db.集合名字.find()`
- 查找一个集合里的带有某一个键的所有数据`db.集合名字.find({键:"值})`
- 比较运算(一般针对数字)
	- $lt   less than
	- $lte  less than equal
	- $gt   greater than
	- $gte  greater than equal
	- $ne   not equal
	现在假设一个集合里我有许多数据有一个名为age的键(对应的值为年龄)，我想筛选出年龄大于18的所有人
	`db.集合名字.find({age:{$gt:18}})`
- 逻辑运算
	- $and
	- $or 
	用法：`db.集合名字.find({$and:[age:{$gt:18},gender:true]})`表示查找所有满足年龄大于18且性别为男的所有数据
- 范围运算 
	- $in
	- $nin
	比如要找年龄是38 48 58的人
	`db.集合名字.find({age:{$in:[38,48,58]}})` 
- 正则表达式 
	$regex
	有两种写法，给定查找的为所有姓李的人(假设该集合有name这个键)
	- `db.集合名字.find({name:{$regex:"^李"}})`
	- `db.集合名字.find({name:/李/)`
	忽略大小写
	- `db.集合名字.find({name:{$regex:"^李",$options:'i'}})`
- 结果显示
	- skip()  跳过几条数据显示
		`db.集合名字.find().skip(3)`
		将跳过前三条数据显示(如果有三条数据的话)
	- limit()  只看匹配的几条数据
		`db.集合名字.find().limit(3)`
		将只显示前三条数据
		需要说明的是skip()的优先级要比limit()高
	- 因为有些数据的键比较多，但是我又只想看其中的某几个键可以用如下语句
		`db.集合名字.find({},{name:1,age:1})`
		这条语句表示显示这个集合的数据里带name和age的键值对
		第一个参数代表这个集合所有数据(猜的)
	- count() 显示匹配的数据个数
		`db.集合名字.find().count()`
	- distinct() 去重操作
		`db.集合名字.distinct('name',{})`
		第一个参数为需要去重的字段，第二个为需要去重的数据

还有一些查找的方法，这里暂不展示了，下面进入与python交互的阶段。

## mongodb与python交互
mongodb与python交互主要用的是**pymongo**的三方库，使用也分几步。分别为建库，建表，然后是对数据的操作。
大致用法如下：
```
import pymongo

mongodb = pymongo.MongoClient()
try:
    collection = mongodb.a.stu

    one = {"name":"xxx","age":33}
    dir = [
        {"name":"张三","age":2},
        {"name":"李四","age":7},
        {"name":"王五","age":6},
        {"name":"小刘","age":5},
        {"name":"小赵","age":4},
        {"name":"小孙","age":3}
    ]

    # collection.insert_one(one)
    # collection.insert_many(dir)

    # collection.delete_one({"name":"xxx"})
    # collection.delete_many({"name":"xxx"})

    collection.update_one({"name":"张三"},{"$set":{"name":"xiaozhang"}})
    collection.update_many({"name":"张三"},{"$set":{"name":"xiaozhang"}})
except Exception as e:
    print(e)
finally:
    mongodb.close()
```
可以发现跟mongodb里的操作大同小异。
接下来可以用python+mongodb搞事情了！！！