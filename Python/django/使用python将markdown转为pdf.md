---
title: 使用python将markdown转为pdf
date: 2019-12-05 10:37:37
tags: python
---
## 写在前头
由于最近在做项目时遇到了一个需求(手机app实现markdown在线编辑)，遂去寻找开源的markdown编辑器，不过最终发现整合到app上太难，于是想将它变成一个在线的网页，需要markdown在线编辑时跳转到相应网页即可，但又苦于组内没有精通前端成员，最终想到了第三种方法，即将markdown转为html，再转pdf，最终实现。
我们遇到这个问题时的情况是：后端向安卓传某篇文章文件链接(后端同样以文件形式存储该文章)，安卓下载相应文件，然后展示。后端用的是django。

## 用到的第三方库或软件
第三方库
	- markdown
	- pdfkit
软件
	[wkhtmltopdf](https://wkhtmltopdf.org/downloads.html)
	这个软件安装要注意操作系统，这里以Windows为例

## 实现
由于我没有找到直接将markdown转为pdf的第三方库，所以这里以html为中转，分布进行。
### markdown转html
```
import codecs,markdown
try:
    input_file = codecs.open('text.md', mode="r", encoding="gb2312")
    text = input_file.read()
    md_html = markdown.markdown(text)
except:
    input_file = codecs.open('text.md', mode="r", encoding="utf-8")
    text = input_file.read()
    md_html = markdown.markdown(text)
```
text.md文件为其同级文件，如果文件再别处，请用绝对路径表示(或相对路径)。这里还应该注意文件的编码格式，可以通过相应的markdown编辑器设置，如果不确定也可以进行分类。其实这里的文件不一定非得要是md文件，我们要的其实只是这个文件里边的内容(将实现了markdown语法的地方进行转换)，如txt类型文件也阔以。
一定要注意编码格式！
一定要注意编码格式！！
一定要注意编码格式！！！
### html转pdf
这里用到的是pdfkit第三方库与wkhtmltopdf。二者的关系可以理解为使用第三方库pdfkit调用wkhtmltopdf，将html转换为pdf文件。个人理解为pdfkit就是一个中转，通过调用不同的方法，使用了这个软件不同的功能。
```
import pdfkit

pdfkit.from_file('text.html','out.pdf')
```
用起来很简单，text.html其同级文件，out.pdf是生成的pdf文件,如果不在同级，请用相对路径或绝对路径(输入的文件也一样)
这个库还有常用两种其他用法:
	`pdfkit.from_string(html,'out.pdf')` 将html字符串转为pdf
	`pdfkit.from_string(url,'out.pdf')` 将url(网址)对应的网页转成pdf

详细可见[pdfkit官方文档](https://pypi.org/project/pdfkit/)