---
title: mysql操作小记
date: 2019-12-22 12:54:34
tags: mysql
categories: database
---

## 数据库的操作 
注:以下命令使用的**dbname**为指代。
- 创建数据库:`create database dbname charset=utf8;`
- 查看所有数据库:`show databases;`
- 删除数据库:`drop database dbname;`]
- 切换数据库:`use dbname;`
- 查看当前数据库:`select database();`

## 表操作
- 创建表(这里以dog为例):
```
create table dog(
id int primary key auto_increment,
name varchar(16) not null
	);
```
	不难发现dog表里要先声明字段名和字段类型(varchar(16)表示最多接受16个字符长度)，再然后是这个字段的其他属性(eg:primary key等)。需要注意的是，每一个字段的末尾要用**','**隔开且最后一个字段末尾不能带**','**否则报错。
- 修改表：基本结构为`alter table tablename;`(tablename为要操作的表名)下边以dog表为例
	- 增加字段`alter table dog add birthday datetime;`，这里增加了一个datetime类型的字段。
	- 查看表的结构(注意不是表的内容):`desc dog;`
	- 修改字段`alter table dog change birthday Birthday int not null`，这里我将birthday修改为Birthday，类型为int，其他属性为not null。
	- 删除字段`alter table dog drop birthday;`，这里删除了B
	irthday字段。
	- 修改表名`rename dog to cat;`将表名为dog的表改为表名为cat的表。
- 删除表
	`drop table tablename;`

- 查看建表时的语句 
	`show create table tablename;`这里可以看到表类型，表字段等信息。可以理解为`desc tablename;`升级版。

## 数据操作
这里假设我有一个名为dog的表，他有id和name字段其中id为primary key auto_increment
- 增加
	- 单条全列插入:`insert into dog values(1,"哈士奇");`(必须按照顺序写)
	- 缺省插入:如果一开始我就把某些字段设置为auto_increment或者有default值等,我插入数据时就可以不用再去管这些字段比如id。`insert into dog(name) values("大金毛");`
	- 批量插入(即插入多条数据):`insert into dog values(3,'泰迪'),(4,"小腊肠");`
	- 批量缺省插入: `insert into dog(name) values("德国牧羊犬"),("狮子狗");`

	如果忘了id到几了 这么写也能完成自增
	`insert into dog values(0,"秋田犬");`
- 查看`select * from dog;`
- 修改
	`update dog set name="秋田犬" where id=1;`
	set后跟要修改的字段，where为筛选条件。需要说明的是这里修改的是所有筛选到的数据，而非第一条。
- 删除
	在数据库里有两种删除，一种为物理删除即把真的把数据库里的数据给删了；另一个为逻辑删除，即给每一个数据一个布尔字段，删除即改变这个布尔值，数据仍在数据库里，只是取出来的时候使用了if判断让用户看不到。
	- 物理删除 `delete from dog where id=1;`
		物理删除原来id为2的数据的id不会变为1
	- 逻辑删除 `update dog set isdelete=1 where id=1;`
		逻辑删除即修改一个布尔值(这里的字段为自己定义的。)

## 查询操作
`select id,name from dog;` 假设dog里有多个字段，这条语句只查询id和dog这两字段
- 条件运算
	- >
	- <
	- >=
	- <=
	- !=
	- =
	`select id from dog where id>2;`
- 逻辑运算 
	- and 
	- or
	- not(一般与in等连用)
	`select id from dog where id>2 and id<5;`
- 模糊查询(类似正则)
	- %			(匹配多个)
	- _			(匹配单个)

	查询名字以金字开头:`select * from dog where name like '金%';`
	查询名字以金字结尾:`select * from dog where name like '%金';`
	查询名字带有金字:`select * from dog where name like '%金$;`
- 范围查询
	- in
	- not in 
	- between and
	查询id为1，3，5，7的人:`select * from dog where id in(1,3,5,7);`
	查询id为1到5之间的人:`select * from dog where id between 1 and 5;`
	查询id为1到5之间且name为哈士奇的人:`select * from dog where id between 1 and 5 and name='哈士奇';`
- 判断空 
	- is 
	- is not
	`select * from dog where name is not null;`

- 优先级 
	- ()
	- not
	- 条件运算
	- 逻辑运算(and > or)
- 聚合运算 
	- count()		个数
		`select count(*) from dog;`
		`select count(id) from dog where name='哈士奇';`
	- max()			最大值
		`select max(id) from dog`
		`select max(id) from dog where name='哈士奇';`
	- min()			最小值
		`select min(id) from dog`
		`select min(id) from dog where name='哈士奇';`
	- sum()			求和
		`select sum(id) from dog`
		`select sum(id) from dog where name='哈士奇';`
	- avg()			平均值
		`select avg(id) from dog`
		`select avg(id) from dog where name='哈士奇';`
- 分组查询
	- group by
	- having
	`select name as '名字',count(*) from dog group by name;`
		通过名字进行分组，名字相同的为一组，逗号之前的内容为别名，没有as 就是原始名字
	`select name,count(*) from dog group by name having name="哈士奇";`
		先分组，然后筛选出哈士奇这一组出来 
- 排序
	- order by
		- asc	升序
		- dasc	降序
		`select id from dog where name="哈士奇" order by asc;`

- 分页
	- limit start,count 	从第几个开始，显示多少数据
	`select * from dog where name="哈士奇" limit 0,2`
- 去重
	- distinct
	`select dintinct name from dog;`

## 外键
基本形式 `foreign key(一张表的某个字段) references 另一张表(某字段);`

下边的命令大致为我创建了三张表,分别为subjects,students和scores，这三张表插入了数据，最后关联外键。
```
mysql> create table subjects(
    -> id int primary key auto_increment,
    -> subject varchar(16) not null
    -> );

mysql> insert into subjects(subject) values("语文"),("数学"),("英语"),("物理"),("化学"),("生物");

mysql> select * from subjects;
+----+---------+
| id | subject |
+----+---------+
|  1 | 语文    |
|  2 | 数学    |
|  3 | 英语    |
|  4 | 物理    |
|  5 | 化学    |
|  6 | 生物    |
+----+---------+

mysql> create table students(
    -> id int primary key auto_increment,
    -> name varchar(16) not null,
    -> isdelete bit default 0,
    -> gender bit
    -> );

mysql> insert into students(name,gender) values("张三",0),("李四",0),("王五",0),("小刘",0),("小孙",1);

mysql> select * from students;
+----+------+----------+--------+
| id | name | isdelete | gender |
+----+------+----------+--------+
|  1 | 张三 |          |        |
|  2 | 李四 |          |        |
|  3 | 王五 |          |        |
|  4 | 小刘 |          |        |
|  5 | 小孙 |          |       |
+----+------+----------+--------+

mmysql> create table scores(
    -> id int primary key auto_increment,
    -> score decimal(5,2),
    -> stuid int,
    -> subid int,
    -> foreign key(stuid) references students(id),
    -> foreign key(subid) references subjects(id)
    -> );

mysql> insert into scores(stuid,subid,score) values(1,1,80),(2,2,99);

mysql> select * from scores;
+----+-------+-------+-------+
| id | score | stuid | subid |
+----+-------+-------+-------+
|  1 | 80.00 |     1 |     1 |
|  2 | 99.00 |     2 |     2 |
+----+-------+-------+-------+
```

## 链接显示
接着要说的是链接显示(这里只用inner join)：个人理解为将有关系的几张表连接在一起，他们应该有一个链接中心(scores),还应该关联其他表(students,subjects)。
例如：
```
mysql> select students.name,subjects.subject,scores.score
    -> from scores
    -> inner join students on scores.stuid=students.id
    -> inner join subjects on scores.subid=subjects.id;
+------+---------+-------+
| name | subject | score |
+------+---------+-------+
| 张三 | 语文    | 80.00 |
| 李四 | 数学    | 99.00 |
+------+---------+-------+
```
## 视图
简单理解为使用查询语句，为查到的数据建一张新表。(封装sqlite语句)
```
mysql> create view test as
    -> select students.name,scores.score from scores
    -> inner join students on scores.stuid=students.id;

mysql> select * from test;
+------+-------+
| name | score |
+------+-------+
| 张三 | 80.00 |
| 李四 | 99.00 |
+------+-------+
```
嘤嘤嘤。

## 自关联
也是一个外键关系，只不过是自己关联自己，这里得具体情况具体分析，但是有一点取别名很重要，即老练运用 as 

## 事务
只有Engine=innodb或Engin=ddb的表才能使用事务功能，可以通过`show create table tablename;`来查看Engine类型(默认为innodb),如果不是可以更改`alter table tablename engine=innodb;`。

- 开启事务 	`begin;`
- 提交事务   `commit;`
- 事务回滚	`rollback;`
开启事务后insert数据实际插入的是缓存区，只有commit后才能在数据库看到他。回滚即把上一次缓存区的数据干掉。

## 索引查询
- 创建索引:`create index indexname on tablename(key(length));`
	indexname为自己定义的索引名
- 查看所有索引:`show index from tablename;`
- 删除索引:`drop index indexname on tablename;`
因为索引查询效率很高所以经常使用，但索引会影响其他性能比如增删改查，会让他们非常的慢。
故咱们的通常做法为**建完就删**。

怎么看查询时间？
- 设置时间: `set profiling=1;`
- 查询语句
- `show profiles;`

## python与mysql交互
使用的第三方库:pymysql
连接数据库:Connect()
Cursor()对象
	- execute()
	- fetchone()
	- fetchall()

```
import pymysql
try:
    # 连接数据库
    con = pymysql.Connect(
        host='localhost',
        port=3306,
        db='animal',
        user='root',
        passwd='8974',
        charset='utf8'
    )

    # 创建游标对象 cursor()
    cur = con.cursor()

    # insert_sub = "insert into subjects(subject) values('爬虫')"
    # cur.execute(insert_sub)

    # update_sub = "update subjects set subject='嘤嘤嘤' where id=8"
    # cur.execute(update_sub)

    # delete_sub = "delete from subjects where id=8"
    # cur.execute(delete_sub)

    select_sub = 'select * from subjects'
    cur.execute(select_sub)

    # 执行过查询语句后在执行如下语句即可查到数据
    print(cur.fetchone())
    print(cur.fetchall())

    con.commit()
    cur.close()
    con.close()
except Exception as e:
    print(e)


```

## 数据库的备份与恢复
备份:`mysqldump -u root -p db_name table_name > 备份文件绝对路径;`
恢复:`mysql -u root -p db_name < 备份文件绝对路径`