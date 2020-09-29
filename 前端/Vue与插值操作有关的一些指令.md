---
title: Vue与插值操作有关的一些指令
date: 2020-02-09 11:15:31
tags: Vue
---
## 写在前头
在vue中提供了一些对于页面 + 数据的更为方便的输出,这些操作就叫做指令, 以v-xxx表示([详细](https://blog.csdn.net/tjx11111/article/details/103606922))。 这里将介绍`v-for`,`v-once`,`v-pre`,`v-text`,`v-html`,`v-cloak`.

## v-for
v-for 指令基于一个数组来渲染一个列表。v-for 指令需要使用 item in items 形式的特殊语法，其中 items 是源数据数组，而 item 则是被迭代的数组元素的别名。([详细](https://cn.vuejs.org/v2/guide/list.html))
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  
  <div id="xxx">
    <ul>
      <li v-for="item in movies">{{ item }}</li>
    </ul>
  </div>
<!-- 将显示
	星际穿越
	大话西游
	盗梦空间
	大圣归来
-->
  <script src="../js/vue.js"></script>
  <script>
    let vu = new Vue({
      el: '#xxx',
      data: {
        movies: ['星际穿越', '大话西游', '盗梦空间', '大圣归来']
      }
    })
  </script>
</body>
</html>
```
仔细观察发现虽然只有一个li标签，但是却显示出了所有数组元素。响应式显示，即我在console控制台为数组追加一个元素，也会即时显示出来。也可以显示当前索引，见上方详细。

## v-once
由于页面是响应式显示，所以我在console改变data里的变量值，页面也会立即发生改变，但是我是用v-once可以保留原始内容。
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="xxx">
    <h2>{{message}}</h2>
    <h2 v-once>{{message}}</h2>
  </div>
  <!-- 开始: 
    hello world!
    hello world!
       在console输入vu.message='page' 后
    page
    hello world!
   -->
  <script src="../js/vue.js"></script>
  <script>
    let vu = new Vue({
      el: "#xxx",
      data: {
        message: 'hello world!'
      }
    })
  </script>
</body>
</html>
```

## v-pre
在html里pre标签为原样显示，那么在这里也是这么个意思。
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="xxx">
    <h2>{{message}}</h2>
    <h2 v-pre>{{message}}</h2>
  </div>
  <!-- 将显示：
    嘤嘤嘤
    {{message}}
   -->
  <script src="../js/vue.js"></script>
  <script>
    let vu = new Vue({
      el: "#xxx",
      data: {
        message: '嘤嘤嘤'
      }
    })
  </script>
</body>
</html>
```

## v-text
即把变量显示成文本，但没有{{}}灵活，仅作了解。
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
  <div id="xxx">
    <h2>{{message}}</h2>
    <h2 v-text="message"></h2>
  </div>
  <!-- 将显示： 
    lalala
    lalala
   -->
  <script src="../js/vue.js"></script>
  <script>
    let vu = new Vue({
      el: '#xxx',
      data: {
        message: 'lalala'
      }
    })
  </script>
</body>
</html>
```

## v-html
当data一变量的值为一标签时，如果想将这个标签给扔到代码里而非页面中的话，v-html可以办这事
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>

  <div id="xxx">
    <h2>{{message}}</h2>
    <h2 v-html="message"></h2>
  </div>
<!-- 
  <a href="http://www.baidu.com">百度</a>
  百度
 -->
  <script src="../js/vue.js"></script>
  <script>
    let vu = new Vue({
      el: '#xxx',
      data: {
        message: '<a href="http://www.baidu.com">百度</a>'
      }
    })
  </script>

</body>
</html>
```

## v-cloak
cloak为斗篷之意。网页在渲染我们的代码时，是从上往下渲染的，即一开始我的`<h2>{{message}}</h2>`在网页里看到的时{{message}}而非其值，当读到script时在开始对其进行替换，此为前提。当我们网络不好时，如果我们不做处理，那么用户最先看到的可能就是{{message}}，过一会才能看到真是内容，这里我们可以用v-cloak来遮住这一不友好操作。
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    [v-cloak] {
      display:none
      };
  </style>

</head>
<body>
  
<div id="xxx">
  <h2 v-cloak>{{message}}</h2>
</div>
<!-- 一开始显示为空白，一秒过后显示application -->
<script src="../js/vue.js"></script>
<script>
  setTimeout( function(){ 
    let vu = new Vue({
      el: '#xxx',
      data: { 
        message: 'application'
      }
    })
  },1000)
</script>
</body>
</html>
```
需要说明的是v-once在Vue渲染后会消除，[Css选择器不懂得戳着](https://www.runoob.com/cssref/css-selectors.html)

