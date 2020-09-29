## 开发环境

- 操作系统：`Windows 10`

- Python版本: `Python3.8`

- Django版本: `Django3`

- IDE: `Pycharm`



## pip换源

由于pip默认使用的是国外的源，这导致安装django时下载速度非常的慢，故有换源一说。这里我使用的是**清华源**。

**Windows**换源方法如下: `C:\Users\用户名`下创建一个名为`pip`的文件夹并在该文件夹中编辑名为`pip.ini`的文件。文件内容如下

```[global]
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn
```

## Django项目创建

- 使用命令行创建。 `win R`输入`cmd`进入终端，进入要创建项目文件夹(在此之前请安装好django库`pip install django`)

> django-admin startproject myproject                        # myproject为自己的项目名(自定义)
>
> cd myproject
>
> python manage.py startapp App					   		 #与上同理，App为应用的名字，可自定义

![命令行创建django项目](C:\Users\flyingdigital\Desktop\随手小记来这里\python\django\img\命令行创建django项目.png)

> 这样项目的主题就有了，但这里我们还得创建`templates`与`static`文件夹(分别存放模板与静态文件)，最终结果如下。

![命令行有templates与static的目录](C:\Users\flyingdigital\Desktop\随手小记来这里\python\django\img\命令行有templates与static的目录.png)

- 使用pycharm创建django项目

  > 使用编辑器就非常方便了，一下均截图演示。（从点击新建项目后开始）

![pycharm新建django项目01](C:\Users\flyingdigital\Desktop\随手小记来这里\python\django\img\pycharm新建django项目01.png)

![pycharm新建django项目02](C:\Users\flyingdigital\Desktop\随手小记来这里\python\django\img\pycharm新建django项目02.png)

![pycharm新建django项目03](C:\Users\flyingdigital\Desktop\随手小记来这里\python\django\img\pycharm新建django项目03.png)



> django项目创建当然是用pycharm更舒服，但pycharm只会默认给你创建一个应用，一旦有多个应用时，命令行就该上场了。

## Django项目配置(setting.py与urls.py)

- setting.py

  ```
  # 需要修改的内容
  ALLOWED_HOSTS = ['*']			#允许所有主机访问
  LANGUAGE_CODE = 'zh-hans'		#中文编码
  TIME_ZONE = 'Asia/Shanghai'		#使用上海时区
  USE_TZ = False					#数据库不适用格林威治时间(相差八小时)
  ```

```
#修改数据库
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'learndjango',					#数据库名字
        'HOST': '127.0.0.1',					#主机地址
        'PORT': 3306,							#端口号	
        'USER': 'root',						
        'PASSWORD': '8974',
    }
}
```

```
#建议在末尾加上的内容

#指明上哪找静态资源(js,css)
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)

# Media files
# https://blog.csdn.net/xxx_gt/article/details/79719790

MEDIA_ROOT = os.path.join(BASE_DIR, "media")
MEDIA_URL = '/media/'  # 跟STATIC_URL类似，指定用户可以通过这个路径找到文件
```

- urls.py

> ```
> #添加如下
> from django.conf.urls.static import static
> 
> # 配置静态页面
> urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
> ```

> 如果是在应用下的子路由中，建议在子路由中加上应用的名空间 ，用于反向解析

```
app_name = 'App' 		# App为应用名
```