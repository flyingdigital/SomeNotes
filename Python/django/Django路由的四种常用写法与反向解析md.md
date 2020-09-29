Django中路由的写法与反向解析

## 路由的四种常用写法



- 最简单不带参数的写法

  ```
  path('handle_data/',views.handle_data,name='handle_data')	   #三个handle_data含义分别为路径名，视图函数名，用于反向查找的name
  ```

  > 这种路由写法不用带任何参数，最为常见

- 带参数的路由

  ```
  # eg1
  path('handle_data/<int:age>/<slug:name>/',views.handle_data,name='handle_data')
  
  # eg2
  path('handle_data/<name>/',views.handle_data,name='handle_data')
  ```

  ```
  #路由转换器(来自官方文档)
  
  
  str - 匹配除了 '/' 之外的非空字符串。如果表达式内不包含转换器，则会默认匹配字符串。
  
  int - 匹配 0 或任何正整数。返回一个 int 。
  
  slug - 匹配任意由 ASCII 字母或数字以及连字符和下划线组成的短标签。比如，building-your-1st-django-site 。
  
  uuid - 匹配一个格式化的 UUID 。为了防止多个 URL 映射到同一个页面，必须包含破折号并且字符都为小写。比如，075194d3-6885-417e-a8a8-6c931e272f00。返回一个 UUID 实例。
  
  path - 匹配非空字段，包括路径分隔符 '/' 。它允许你匹配完整的 URL 路径而不是像 str 那样匹配 URL 的一部分。
  ```

  > 如果路由这么些那么相应的handle_data应该如下定义

  ```
  # eg1
  def handle_data(request,age,name):
  	...
  	return None
  
  # 举例说明： 如果访问的是127.0.0.1/handle_data/12/tom/
  那么这里离的age，name分别为12，tom  eg2 同理
  ```

  

- 使用正则表达式的路由

  > 命名正则表达式组的语法是 `(?P<name>pattern)` ，其中 `name` 是组名，`pattern` 是要匹配的模式。
  >
  > 它所对应的视图函数也与带参数路由类似

  ```
  re_path(r"articles/?P<year>[0-9]{4}/",views.handle_articles,name="articles")
  ```

  

  

- include()子路由的用法

  > 由于django项目可以有多个应用，所有的应用全都堆积在总路由中，会非常臃肿，那么我们可以每个应用中创建一个子路由且在总路由中包含这些子路由。用法如下

  ```
  # 总路由写法,假设有一个应用名为App
  
  path('App/',include("App.urls"))		# 第一个参数是个这个名为App的应用下的所有子路由添加一个路由前缀(也可以不加)，第二个参数则是在总路由中包含子路由，除此外子路由写法与总路由无太大区别
  ```

  

## 路由的反向解析

来自官方的解释

- 从用户/浏览器请求的 URL 开始，它调用正确的Django视图，并从 URL 中提取它的参数需要的值。
- 从相应的 Django 视图标识以及要传递给它的参数来获取相关联的 URL 。



这里所谓的反向解析就与上边子路由中的**app_name**及每个路由的**name**有很大关系了



路由的反向解析一般与重定向一起使用,重定向的函数为`redirect()`或`HttpResponseRedirect()`,可以理解为redirect是HttpResponseRedirect的快捷方式，毕竟后者名字太长。。。使用到的反向解析函数为`reverse()`.



- 不带参数路由的反向解析

  ```
  #一条路由为
  path('handle_data/',views.handle_data,name='handle_data')
  #另一条路由为
  path('index/',views.index,name='index')
  
  #我们要做的事情是访问index的时候重定向到handle_data
  ```

  ```
  #视图函数的写法
  def index(request):
  	return HttpResponseRedirect(reverse("App:handle_data"))
  	
  def handle_data(request):
  	return HttpResponse("handle_data")
  ```

  

- 带参数路由的反向解析(此处只介绍关键字参数)

  ```
  #一条路由为
  path('handle_data/<int:age>/',views.handle_data,name='handle_data')
  #另一条路由为
  path('index/',views.index,name='index')
  
  #我们要做的事情是访问index的时候重定向到handle_data
  ```

  ```
  #视图函数的写法
  def index(request):
  	return HttpResponseRedirect(reverse("App:handle_data",kwargs={'age':12}))
  	
  def handle_data(request):
  	return HttpResponse("handle_data")
  ```

  > 其实就是把关键字传给这个路由