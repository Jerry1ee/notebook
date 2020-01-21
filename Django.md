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

和类的概念差不多，包含一个实体的属性，动作等

在我们创建的图书馆应用中，需要创建一个模型Book。Book模型包括四个字段：书名、作者、出版社、出版日期。

向mysite/lib/models.py文件中写入如下代码：
```
# lib/models.py
from django.db import models

class Book(models.Model):
    name = models.CharField(max_length=200)
    author = models.CharField(max_length=100)
    pub_house = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
```

### 激活模型
- 首先将lib应用安装到项目中
```
# mysite/mysite/settings.py
INSTALLED_APPS = [
    'lib.apps.LibConfig',
    'django.contrib.admin',
    ...
]
```
- 现在你的项目已经包含了 lib 应用，运行下面的命令：

```
python manage.py makemigrations lib
```
- 通过运行 makemigrations 命令，Django 会检测你对模型文件的修改，并且把修改的部分储存为一次迁移。
- 查看迁移命令执行的语句
```
python3 manage.py sqlmigrate lib 0001
```

- 现在运行migrate命令，在数据库里创建新定义的模型的数据表：
```
python manage.py migrate 
```
在数据库中表已经创建完毕了
[此处应有张图]

### 使用API

以下语句可以在数据库中进行条目创建

```
>>>from lib.models import Book
>>>Book.objects.all()   #获取Book所有对象
<QuerySet []>
>>>from django.utils import timezone
>>>b = Book(name='Business', author='Tom', pub_house='First Press', pub_date=timezone.now())    #创建
>>>b.save() #保存
>>>b.id
1
>>>b.name
'Business'
>>>b.pub_date
datetime.datetime(2018, 7, 4, 2, 29, 7, 578323, tzinfo=<UTC>)
```


## 模板

### 创建模板
- 在你的 mysite/lib 目录里创建一个 templates 目录。Django 将会在这个目录里查找模板文件。

- 在 mysite/mysite/settings.py 文件中的 TEMPLATES 配置项描述了 Django 如何载入和渲染模板。默认的设置文件设置了 DjangoTemplates 后端，并设置 APP_DIRS = True。这一选项将会让 DjangoTemplates 在每个 INSTALLED_APPS 文件夹中寻找 templates 子目录。

- 新建模板文件 lib/templates/lib/detail.html ，并向其中写入如下代码：
```
# lib/templates/lib/detail.html
<h1>Book List</h1>
<table>
    <tr>
        <td>书名</td>
        <td>作者</td>
        <td>出版社</td>
        <td>出版时间</td>
    </tr>
{% for book in book_list.all %}
    <tr>
        <td>{{ book.name }}</td>
        <td>{{ book.author }}</td>
        <td>{{ book.pub_house }}</td>
        <td>{{ book.pub_date }}</td>
    </tr>
{% endfor %}
</table>
```
- 模板统一使用点符号.来访问变量的属性。
- 在示例 {{ book.name }} 中，首先 Django 尝试对 book 对象使用字典查找（也就是使用 obj.get(str) 操作），如果失败了就尝试属性查找（也就是 obj.str 操作），结果是成功了。如果这一操作也失败的话，将会尝试列表查找（也就是 obj[int] 操作）。

- 在 {% for ... in ... %} 循环中发生的函数调用：book_list.all 被解释为 Python 代码 book_list.objects.all() ，将会返回一个可迭代的 Book 对象，这一对象可以在 {% for ... in ... %} 标签内部使用。

### 创建视图

现在我们要创建视图来返回图书列表：
```
# mysite/lib/views.py
from django.shortcuts import render
from .models import Book

def detail(request):
    book_list = Book.objects.order_by('-pub_date')[:5]
    context = {'book_list': book_list}
    return render(request, 'lib/detail.html', context)
```

- 在此视图函数detail中，首先将数据库的Book列表按照pub_date时间来排序，存储到变量book_list中。

- 「载入模板，填充上下文，再返回由它生成的 HttpResponse 对象」是一个非常常用的操作流程。于是 Django 提供了一个快捷函数render()。

- render()函数把request对象作为它的第一个参数，模板作为第二个参数，字典作为它的可选的第三个参数。它返回给定模板呈现的给定文本的一个HttpResponse对象。

- 在这里，context信息将会返回到模板lib/detail.html。

### 绑定链接

将新视图添加进lib.urls模块里：
```
# lib/urls.py
from django.urls import path

from . import views

urlpatterns = [
    path('', views.index, name='index'),
    path('detail/', views.detail, name='detail'),
]
```
python manage.py runserver 访问 localhost:8000/lib/detail 可以看到图书列表


## MTV模式的增删改查

### 设计表单
简单前端提交表单，在上一个实验中的lib/templates/lib/detail.html里，添加一个表单。
```
<form action="{% url 'addBook' %}" method="post" name="addBook">
    {% csrf_token %}
    <p><span>书名：</span><input type="text"  name="name"></p>
    <p><span>作者：</span><input type="text"  name="author"></p>
    <p><span>出版社：</span><input type="text"  name="pub_house"></p>
    <input type="submit" value="添加">
</form>
```
### 命名空间

- 教程项目只有一个应用lib 。在一个真实的 Django 项目中，可能会有五个，十个，二十个，甚至更多应用。Django 如何分辨重名的 URL 呢？

- 举个例子，lib 应用有 detail 视图，可能另一个博客应用也有同名的视图。Django 如何知道 {% url %} 标签到底对应哪一个应用的 URL 呢？

- 答案是：在根 URLconf 中添加命名空间。在 lib/urls.py 文件中稍作修改，加上 app_name 设置命名空间：
```
from django.urls import path

from . import views

app_name = 'lib'    #添加这行
urlpatterns = [
    path('', views.index, name='index'),
    path('detail/', views.detail, name='detail'),
]
```
现在回到 lib/templates/lib/detail.html 更改action：
```
<form action="{% url 'lib:addBook' %}" method="post" name="addBook">
```

### 实现添加书籍函数

- 在lib/urls.py里添加URL地址映射：
```
# lib/urls.py
path('addBook/', views.addBook, name='addBook'),
```
然后创建addBook函数来实现我们添加书籍的功能。

将下面的代码添加到lib/views.py：
```
# lib/views.py
from django.http import HttpResponseRedirect
from django.urls import reverse

def addBook(request):
    if request.method == 'POST':
        temp_name = request.POST['name']
        temp_author = request.POST['author']
        temp_pub_house = request.POST['pub_house']

    from django.utils import timezone
    temp_book = Book(name=temp_name, author=temp_author, pub_house=temp_pub_house, pub_date=timezone.now())
    temp_book.save()

    #重定向
    return HttpResponseRedirect(reverse('lib:detail'))
```
简单说明：

request.POST 是一个类字典对象，可以通过关键字的名字获取提交的数据。 这个例子中，request.POST['name'] 以字符串形式返回name的值。 request.POST 的值永远是字符串。

在添加书籍之后，代码返回一个 HttpResponseRedirect 而不是常用的 HttpResponse, HttpResponseRedirect 只接收一个参数：用户将要被重定向的 URL。

你应该在每次处理POST数据时，都返回HttpResponseRedirect 。这也不是 Django 的特定技巧，这是优秀的网站开发的实践。

在这个例子中，我们在 HttpResponseRedirect 的构造函数中使用 reverse() 函数。这个函数避免了我们在视图函数中硬编码 URL。它需要我们给出想要跳转的视图的名字和该视图所对应的 URL 模式中需要给该视图提供的参数。 reverse()调用后将返回这样一个字符串：/lib/detail/.

添加书籍功能完成，现在可以随意添加书籍。
