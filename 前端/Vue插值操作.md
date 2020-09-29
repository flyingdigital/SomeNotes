---
title: Vue插值操作
date: 2020-02-08 22:02:04
tags: Vue
---
## 简单介绍
Vue里的插值操作为Mustache(胡须)语法，表现形式为**{{}}**(俩大括号里)。一般使用时预先在Vue的data里定义好model，需要使用时再将model填进{{}}里。

## 使用示例(请自行导入Vue)
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
        <h2>{{frist_name}} {{last_name}}</h2>
        <h2>{{frist_name + ' ' + last_name}}</h2> 
        <h2>{{ 2 * num }}</h2>      
        <!-- 插值操作可以进行简单运算 -->
        <!--    将显示以下内容： 
            hello world     
            qian zhao
            qian zhao
            20
        -->
    </div>

    <script src="../js/vue.js"></script>
    <script>
        const vu = new Vue({
            el: "#xxx",     //此为element之意，即要挂在管理元素
            data: {
                message: 'hello world',
                frist_name: 'qian',
                last_name: 'zhao',
                num: 10,
            }               //data里的东西个人理解为类似变量的东西
        })
    </script>
</body>
</html>
```