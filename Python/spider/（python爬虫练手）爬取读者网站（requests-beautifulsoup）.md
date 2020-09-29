---
title: （python爬虫练手）爬取读者网站（requests+beautifulsoup
date: 2019-09-17 22:29:44
tags: Python Spider
---
## 文章简介
这篇文章主要是对python爬虫项目的一个小练习，会使用到的python第三方库为`requests`与`BeautifulSoup`。爬取的对象为读者文摘部分内容。

## 爬取一篇文章
我先爬取一篇文章，为后边爬取多篇文章打基础。
这里我采用的方式是先把代码贴出来，再做解读。![python爬虫_读者文摘_爬取一篇文章.png](https://i.loli.net/2019/09/17/A9v7kaZod4RjJYu.png)
```
import requests
from bs4 import BeautifulSoup
```
这里是导入requests库与BeautifulSoup库。
```
url = 'http://www.rensheng5.com/duzhewenzhai/rensheng/id-176946.html'
response = requests.get(url)
```
*url*即我们要爬取文章的网址，第二行代码`response = requests.get(url)`调用了`requests`里的`get`方法将url作为参数传进，它将返回一个网页的响应体，这个响应体即包含了我们接下来要操作的内容。(这里可以做一个小实验，比如直接`print(response)`，这时打印出来的会是`<Response [200]>`，这个200我理解为网页的状态码，即连通了网站)
```
html = response.content.decode('gbk')
```
这里我们也可以做一个尝试，比如在这行代码之前`print(response.content)`或者`print(response.text)`,前者会返回一个网页源代码的二进制形式![python爬虫_读者文摘_response.content内容.png](https://i.loli.net/2019/09/17/9crNZtuVDL7wlPA.png)而后者会返回一个带缩进的网页源代码。那接着咱们再回到前边这行代码，这行代码主要是改变源代码的编码格式为*gbk*，因为不这么干的话，汉字有可能出现乱码。
```
soup = BeautifulSoup(html,'lxml')
```
这里我调用了BeautifulSoup，第一个参数为指定编码格式的源代码，而第二个`lxml`是一个html的解析器(这玩意要自己安装`pip install lxml`,如果比较懒，可以使用自带的解析器`html.parse`),说了这么多，这玩意有啥用呢？把它打印一下就可以明显看出端倪`print(soup)`,这里打印出的网页源代码的缩进没有了，目的是为了后边更好的筛选所需内容。
```
print(soup.find('div',class_="artview").find('h1').get_text())
print(soup.find('div',class_="artbody").find('p').get_text())
```
这两行是我要找的内容，`find()`方法是找到第一个满足条件的式子，与之对应的是`find_all()`方法，它会匹配所有满足条件的式子。它们里边的参数大致相同，第一个参数为标签名，第二个是它所对应的的属性,由于`class`是python的关键字，所以在这后边加一下划线来替代。如果做到了这一步不调用后边的`get_text()`方法，那么我们最终打印的会是一个带标签的式子，而这里的`get_text()`方法就是获取标签里边文本的操作。
致辞，单个文章爬取完成。

## 爬取多篇文章

这里我来先捋捋思路，我爬取一篇文章的时候是先有了这篇文章的网址，接着爬取。而对文章而言一般都会有个类似目录的玩意，这上边会有各个文章连链接，简单查看就能知道他们的链接在a标签的href属性里边，也就是说我们只要从总目录里先获取每一个文章的链接，接着进行访问，重复爬取一篇文章的思路就行了。奈斯，我们开始动手吧。![python爬虫_读者文摘_爬取多篇文章.png](https://i.loli.net/2019/09/17/GYgswdpbryTDoJ7.png)
```
def getSoup(url):
    response = requests.get(url)
    html = response.content.decode('gbk')
    soup = BeautifulSoup(html,'lxml')
    return soup
```
这里我定义了一个函数`getSoup(url)`,它实现的功能是获取传入url的源代码并对其进行解析，最后把解析好的源代码返回。
```
def main(url):
    soup = getSoup(url)
    lis = soup.find('ul',class_="p7").find_all('li')
    for li in lis:
        href = li.find('a')['href']
        text_soup = getSoup(href)
        title = text_soup.find('div',class_="artview").find('h1').get_text()
        text = text_soup.find('div',class_="artbody").find('p').get_text()
        print(href + '\n' + title + '\n' + text)
```
这个主函数实现的内容也比较清楚，首先将传入的url进行解析(这里的url是目录的网址，即包含所有文章链接的网址)，接着我根据网页的源代码筛选出我需要的标签[得到的是一个列表]，根据我的需要获取href属性的值(这里的值就是每一篇文章的链接)，接着我把每一篇文章的链接传给getSoup()函数返回解析后的(文章)源代码，接下来的操作就是对单篇文章进行爬取了，最后进行打印。
```
if __name__ == '__main__':
    url = 'http://www.rensheng5.com/duzhewenzhai/'
    main(url)
```
这里主要是传入目录的url，并调用main函数

至此，多篇文章爬取完成。
具体的网站内容可以访问我所使用的url，单篇文章：[http://www.rensheng5.com/duzhewenzhai/rensheng/id-176946.html](http://www.rensheng5.com/duzhewenzhai/rensheng/id-176946.html).多篇文章：[http://www.rensheng5.com/duzhewenzhai/](http://www.rensheng5.com/duzhewenzhai/).