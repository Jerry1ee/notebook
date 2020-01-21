## 安装
```
控制台 pip install django

命令行 print(django.get_version())检查django 版本
```
## 创建一个例子项目
cd 相应文件夹下
```
django-admin startproject mysite
```
- 外层的mysite/：是项目的容器，可以为任意名字。
- manage.py：一种让你可以使用各种方式管理Django项目的命令行工具。在mysite/目录下输入python3 manage.py help，看一看它都能做什么。
- 内层的mysite/：包含项目，是一个纯Python包。你可以在包里调用它内部的任何东西。
- __init__.py：一个空文件，告诉Python这个目录应该被认为是一个Python包。一般，你不需要去修改它。
- settings.py：Django项目的配置文件。
- urls.py：Django项目的URL声明。
- wsgi.py：作为项目的运行在WSGI兼容的Web服务器的入口。

## 运行这个例子
在相应的文件夹下，运行命令
```
python manage.py runserver
```

## 构建一个app
```
python manage.py startapp lib # 应用名为lib
```

目录结构
```
lib/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```
在 views.py中编写视图
```
from django.shortcuts import render
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello World!")
```
新建 lib/urls.py，输入以下代码
```
# lib/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```
对 path函数的解释

|参数|意义|是否必须|
|:---|:----|:-|
|route|route 是一个匹配 URL 的准则（类似正则表达式）。当 Django 响应一个请求时，它会从 urlpatterns 的第一项开始，按顺序依次匹配列表中的项，直到找到匹配的项。|必须|
|view|当 Django 找到了一个匹配的准则，就会调用这个特定的视图函数，并传入一个 HttpRequest 对象作为第一个参数，被“捕获”的参数以关键字参数的形式传入。|必须|
|kwargs|任意个关键字参数可以作为一个字典传递给目标视图函数。|可选|
|name|为你的 URL 取名能使你在 Django 的任意地方唯一地引用它，尤其是在模板中。这个有用的特性允许你只改一个文件就能全局地修改某个 URL 模式。|可选|

在上面的代码中，route为空意味着我们可以直接用链接http://localhost:8000/lib/  访问该视图函数，view=view.index是调用了view.py中的index视图，name为index代表我们可以在模板中用index来引用返回的变量。

下面的代码，将lib的视图 引入 mysite中，include()函数允许使用其他应用的URLconf

```
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('lib/', include('lib.urls')),
    path('admin/', admin.site.urls),
]
```

## 使用模型

### 配置数据库
默认使用 sqlite作为默认数据库，可以使用MySQL等其他数据库，更改配置时，需要修改文件settings.py，官方文档说明如下：
```
When connecting to other database backends, such as MySQL, Oracle, or PostgreSQL, additional connection parameters will be required.
See the ENGINE setting below on how to specify other database types. This example is for PostgreSQL:

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```
更改时区 TIME_ZONE = 'Asia/Shanghai'

### 创建模型



