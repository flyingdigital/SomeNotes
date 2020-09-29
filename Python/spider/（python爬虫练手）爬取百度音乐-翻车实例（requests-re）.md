---
title: （python爬虫练手）爬取百度音乐--翻车实例（requests+re）
date: 2019-09-19 15:16:09
tags: Python Spider
---

## 文章简介
这篇文章是来爬取百度音乐的歌曲的，本来看半年前的视频时是可以爬取vip音乐的接口的，但是现在却发现这个vip音乐的接口里边全是错误。心里那个不爽哟。但这也从另一个角度告诉我们技术要时常更新，不然就out了。。。。

## 爬取一首歌
只针对一首歌可以模仿梨视频的操作![python爬虫_百度视频_爬取一首歌的地址.png](https://i.loli.net/2019/09/19/7RPABWbzKOgUQGw.png)![python爬虫_百度视频_爬取一首歌.png](https://i.loli.net/2019/09/19/tLXwVNjSElFaRM2.png)
```
url = 'http://audio02.dmhmusic.com/71_53_T10045991216_128_4_1_0_sdk-cpm/cn/0207/M00/7B/92/ChR461z86tSAKx30AEiissuSids044.mp3?xcode=079bc3704de9af1351a5828c0637a6378f5fe73'

response = requests.get(url)
```
这两行代码即获取这个音乐的真实地址的源代码。
```
with open('小半.mp3','wb') as f:
    f.write(response.content)
```
这两行代码即以二进制保存这个音乐。感jo还行，接下来我们来爬取多个音乐。
至此，一首歌爬取完毕。

## 爬取多首歌
这里与爬取梨视频的操作有些不一样，我们回顾一波梨视频的做法，我们找到这个视频的地址，接着我们在源代码里找到了这个地址，然后以这个为基础进行一系列操作。但是这个操作再这个程序里行不通。所以另找方向。不卖关子了，这里随便找两音乐，对比一波他们真实地址的异同这里我随意找了两个
```
http://audio02.dmhmusic.com/71_53_T10046145023_128_4_1_0_sdk-cpm/cn/0208/M00/67/1B/ChR461shSbeAdAWeAFB5VDzrIFc155.mp3?xcode=49e1b2339ff3225a51b5df12a0b174c0bef9e61

http://audio02.dmhmusic.com/71_53_T10045991216_128_4_1_0_sdk-cpm/cn/0207/M00/7B/92/ChR461z86tSAKx30AEiissuSids044.mp3?xcode=079bc3704de9af1351a5828c0637a6378f5fe73
```
发现他们后边的xcode参数比较唯一，于是我们在网页里打开开发者工具并进入到network属性里边，发者xcode参数值并`control f`寻找这段参数。发现找到的内容里有一个的请求头是一个api
![python爬虫_百度视频_api接口.png](https://i.loli.net/2019/09/19/GWRPHMYi7QOqxum.png)
我们进入这个接口发现里边全是json数据，但是这个时候去print是无法打印出来的，因为里边有一段多的代码，这段代码并不是json数据。不要慌，这个时候一定会有种感jo，这个api太长了，我们能不能删掉一些参数，让它变短，可以，最终的结果为`http://musicapi.taihe.com/v1/restserver/ting?method=baidu.ting.song.playAAC&songid=xxxxxx`,而且那一段多余的代码也被删除了，简直爽到飞起。这里建议导入一个模块`import pprint`用它来输出json数据会比较好看.欲知为何飞起，请看下边代码。 
![python爬虫_百度视频_爬取多首歌.png](https://i.loli.net/2019/09/19/dqhsmcRVkgaxvCf.png)
```
url = 'http://music.taihe.com/artist/7994'
response = requests.get(url)
list = re.findall('class="songname".*?<a href="/song/(.*?)"',response.text, re.S)
```
这里相当于爬取主页面的歌曲的`singid`，为后边访问接口做准备.
```
for l in list:
    url_api = 'http://musicapi.taihe.com/v1/restserver/ting?method=baidu.ting.song.playAAC&songid=' + l
    resp = requests.get(url_api)
    json = resp.json()
    # pprint.pprint(json)
    if 'bitrate' in json.keys():
        music = json['bitrate']['file_link']
        music_name = json['songinfo']['title']
        with open(music_name+".aac",'wb') as f:
            f.write(requests.get(music).content)
            print(music_name)
        print(music,music_name  )
    else:
        continue
```
我们可以使用`pprint。pprint(json)`看一波json数据
![python爬虫_百度视频_json数据.png](https://i.loli.net/2019/09/19/kfoOw5QCxiRP8hv.png)
发现这里的链接，就是这个歌曲的真是链接。ok，接下来咱就爬就完事了。
```
    url_api = 'http://musicapi.taihe.com/v1/restserver/ting?method=baidu.ting.song.playAAC&songid=' + l
    resp = requests.get(url_api)
    json = resp.json()
```
这里是获取api的json数据。

```
	music = json['bitrate']['file_link']
    music_name = json['songinfo']['title']
    with open(music_name+".aac",'wb') as f:
        f.write(requests.get(music).content)
        print(music_name)
```
这里是获取json数据里的歌曲真是的值的链接和歌曲的名字，并将它保存下来，`print(music_name)`是为了在程序运行时好歹看到一些东西，不然会有些难过。但是我这么干了之后，发现爬了一会就报错了，说是找不到啥啥啥，所以我加入了if语句来判断这个键在不在这个字典里。(json数据挺像python的字典的，特意`print(type(json)`,打印的是dict)。但是尴尬的事情来了，因为我爬的是周董的歌(众所周知，周董的歌大都要vip)，所以我这些vip的内容我全没爬到，桑心，暂时没解决。