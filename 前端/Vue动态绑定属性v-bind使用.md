---
title: Vue动态绑定属性v-bind使用
date: 2020-02-10 09:33:23
tags: Vue
---
## 简单介绍
前边的插值操作是针对标签内的文本(<h2>文本</h2>)，而v-bind针对的是标签属性（如class，id等）里的值。即此指令可以将data里的变量与标签属性值进行动态绑定。

语法糖为`:`

## 简单使用
哪个属性需要动态绑定就在哪个属性前加上`v-bind:`,这是属性值可表示为data里的变量值。
需要注意的是如果一个属性前边有`v-bind:`但属性值red在data里找不到，那么将不显示该属性值，并会在控制台警告。
还需注意: red为变量,'red'为red。

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .change_to_red {
      color: red;
    }
  </style>
</head>
<body>
  <div id="xxx" v-bind:class="red">
    <h2>{{message}}</h2>
  </div>
<!-- 将显示一个红色的你好啊！
<div id="xxx" class="change_to_red"><h2>你好啊！</h2></div>
 -->

  <script src="../js/vue.js"></script>
  <script>
    let vu = new Vue({
      el: '#xxx',
      data: {
        red: 'change_to_red',
        message: '你好啊！'
      }
    })
  </script>
</body>
</html>
```

## 对象语法(常用)
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <style>
    .RedColor {
      color: red;
    }
    .GreenColor {
      color: green;
    }
  </style>
</head>
<body>
  <div id="xxx" v-bind:class="{'RedColor': isRed,'GreenColor': isGreen}">
    <h2>{{message}}</h2>
  </div>
<!-- 将显示一个绿色的你好鸭！
<div id="xxx" class="GreenColor"><h2>你好鸭！</h2></div>
 -->
  <script src="../js/vue.js"></script>
  <script>
    let vu = new Vue({
      el: '#xxx',
      data: {
        isRed: false,
        isGreen: true,
        message: '你好鸭！'
      }
    })
  </script>
</body>
</html>
```
发现在class后加入的值为**{key1: value1,key2: value2}**形式的(python里管它叫字典)，需要说明的是这里加入的是一个对象，可以是变量，可以是函数，可也是数组等等。

## 数组语法 
即将一个数组添加上去,不常用。
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>
<body>
   <div id="xxx" v-bind:class="cls">
     <h2>{{message}}</h2>
   </div>
<!-- <div id="xxx" class="red green blue"><h2>你好鸭！</h2></div> -->
   <script src="../js/vue.js"></script>
   <script>
     let vu = new Vue({
       el: '#xxx',
       data: {
         cls: ['red','green', 'blue'],
         message: '你好鸭！'
       }
     })
   </script>
</body>
</html>
```