---
title: （python爬虫练手）爬取剑来小说（requests+pyquery）
date: 2019-09-19 08:12:06
tags: Python Spider
---
## 文章简介
这篇文章主要爬取[剑来小说](http://www.jianlaixiaoshuo.com/),大致的原理方法跟上篇爬取读者文摘的文章类似。

## 爬取小说
![python爬虫_剑来小说_爬取小说.png](https://i.loli.net/2019/09/19/TiIM6pBv1gfRsnZ.png)
```
import requests
from pyquery import PyQuery as pq
```
这里我导入了requests库(网页请求)和pyquery库(解析)。为啥没有用BeautifulSoup？下边是一个博主对python四大主流解析库的性能测试([原文地址](https://www.cnblogs.com/zhangyafei/p/10521310.html))![主流解析库的比较.png](https://i.loli.net/2019/09/19/s1QV7gbO9YcXrai.png)
可以发现，beautifulsoup非常的慢，所以建议多学几个解析库，以备不时之需。
```
url = 'http://www.jianlaixiaoshuo.com/'
response = requests.get(url)
response.encoding = response.apparent_encoding
```
这三行分是获取这个url的网页源代码，并将它进行一个转码。
```
html = pq(response.text)
titles = html('dl > dd   a')
```
这两行就是pyquery的语法了，`html = pq(response.text)`即解析这个网页的源代码，`titles = html('dl > dd   a')`是来筛选每一个文章的地址(其实获取的是一个a标签，每个文章的地址将在遍历时得到)，这里返回的是类型为`<class 'pyquery.pyquery.PyQuery'>`的许多对象(可以使用`print(type(titles)`查看)，所以我们在下边遍历时要使用`items()`，即取出每一个对象。
```
for title in titles.items():
    newurl = 'http://www.jianlaixiaoshuo.com/' + title.attr.href
    newresponse = requests.get(newurl)
    newresponse.encoding = newresponse.apparent_encoding
    newhtml = pq(newresponse.text)
    newtitle = newhtml('#BookCon > h1').text()
    newtext = newhtml('#BookText > p').text()
    print(newtitle)
    if newtext == '':
        newtext = newhtml('#readerFs > p').text()
    print(newtext)

```
这里的`title.attr.href`是获取title里的href属性值，构成一个新的url，这个即每章小说的地址。接下来的操作就是在单篇文章里获取章节名与文章内容了，不再多述。关于最后的if语句，在爬取时我发现有些章节内容为空，于是我进去查看，发现我筛选的内容里(newtext)居然没啥东西，而真正的文章却出现在了`id="readerFs"`的标签下，故有此判断。

## 没有解决的问题
爬取小说的时候我发现有些章节出现了乱码的情况，但是代码应该没问题，暂未解决此问题。

## pyquery的一些用法介绍
要说的是pyquery的一些用法，筛选时`.`表示`class`属性，而`#`则表示的是`id`属性(`newhtml('#BookText > p')`)，`a > b`表示a标签下所有标签为b的子节点，而`a  b`表示a标签下所有标签为b的子孙节点。`text()`方法是获取这个标签里边的文本(如果`print(type(newtext))`就会发现使用`text()`方法得到的类型为*str*).