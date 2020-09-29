---
title: （python爬虫练手）爬取梨视频（requests+re）
date: 2019-09-19 10:05:44
tags:  Python Spider
---

## 文章简介
这篇文章主要是对[梨视频](https://www.pearvideo.com/)的爬取。

## 爬取单个视频
点开梨视频的某一个视频，发现是可以播放的，但是它并没有提供下载的按钮。打开查看网页的源代码，点击`network`属性，筛选`media`内容(这里可能需要先点击一下视频，及让其播放，这样就可以从服务器端得到它传回来的视频的真实地址)![python爬虫_梨视频_爬取多个视频_得到单个视频的真实地址.png](https://i.loli.net/2019/09/19/1LPKoWnD93Hv5qm.png)
访问这个地址就可以看到这个视频，接着我们就开始对这单个视频进行爬取。![python爬虫_梨视频_单个视频.png](https://i.loli.net/2019/09/19/7gNGzQ2VyU4uhpr.png)
```
with open('0.0.mp4','wb') as f:
    f.write(response.content)
```
由于视频，图像，音乐均为二进制存储，而`response.content`将返回一个二进制源代码。`open('0.0.mp4','wb')`这个是创建一个名为`0.0.MP4`的文件(若已经有这个文件存在则重写这个文件),`wb`表示以二进制形式写入。
至此，单个视频爬取完毕。

## 爬取多个视频
这里回顾一下单个视频爬取方法，先认为的打开一个视频，然后再开用发者工具找到从服务端返回的视频地址，接着进行爬取。但爬取多个视频就不能这么认为操作了，毕竟计算机可不知道怎么搞。这里是无意中在梨视频网页的源代码中找到了这个视频的真实地址(这里只是巧合，不可能所有的视频都这么干，但在这里这条路竟然走得通，那咱就该坚定不移的走下去，其实就是我现在不会别的操作)。![python爬虫_梨视频_爬取单个视频_从网页源代码得到单个视频的真实地址.png](https://i.loli.net/2019/09/19/esQjSa7dHxWBEvy.png)
ok，我们再来捋一捋爬取多个视频的思路，先从一个类似小说目录的网页(可以看到多个视频)找到每一个视频链接，从这个链接的源代码里找到这个视频的真实地址，再进行下载，奈斯，接下来咱们开始。![python爬虫_梨视频_多个视频.png](https://i.loli.net/2019/09/19/SRJ6Ikpw2Qj3fez.png)
```
response = requests.get('https://www.pearvideo.com/category_8')
url_list = re.findall('class="vervideo-bd".*?href="(.*?)"', response.text,re.S)
```
这个是请求得到视频首页的响应体并用正则表达式找到网页链接。这里为啥要用正则？因为这里的链接存在与一串字符串里而不再标签里，所以用其他的解析库pyquery，xpath等没办法得到。`findall()`方法将返回一个列表，列表里的内容是每一个满足条件的类容，即用（）括起来的，`re.S`即可匹配多行
```
for u in url_list:
    url = 'https://www.pearvideo.com/' + u
    resp = requests.get(url)
    name = re.findall('<h1 class="video-tt">(.*?)</h1>',resp.text,re.S)[0]
    video_url = re.findall('srcUrl="(.*?)"',resp.text,re.S)[0]
    video_content = requests.get(video_url).content
    with open(name+".mp4",'wb') as f:
        f.write(video_content)
```
这里的操作就是得到该视频的页面，并从这个页面里找到这个视频的名字，整整存储视频的地址，接着下载就完事了。
至此，多个视频爬取完毕。