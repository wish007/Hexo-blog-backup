---
title: 搭建Django博客（3）网页处理逻辑
date: 2016-09-05 21:18:32
categories: Django
tags:
- Django
---

# 网页程序的逻辑

# 前言

> 把数据存取逻辑、业务逻辑和表现逻辑组合在一起的概念有时被称为软件架构的 Model-View-Controller(MVC) 模式。
>
> Model 代表数据存取层，View 代表的是系统中选择显示什么和怎么显示的部分，Controller 指的是系统中根据用户输入并视需要访问模型，以决定使用哪个视图的那部分。

Django 也遵循这种 MVC 开发模式，下面是 Django 所对应的 MVC：

- M  数据存取部分，由 Django 数据库层处理。
- V  选择哪些数据要显示以及怎样显示的部分，由视图和模板处理。
- C  根据用户输入分配视图的部分，由 Django 框架根据 URLconf 设置，对给定的 URL 调用适当的函数。

由于 Django 里面 C 由框架自行处理，而 Django 里更关注的是模型（Model）、模板（Template）和视图（Views），所以 Django 也被称为 MTV 框架 。在 MTV 开发模式中：

- M  代表模型（Model），即数据存取层。 该层处理与数据相关的所有事务： 如何存取、如何验证有效性、包含哪些行为以及数据之间的关系等。
- T  代表模板（Template），即表现层。 该层处理与表现相关的决定： 如何在页面或其他类型文档中进行显示。
- V  代表视图（Views），即业务逻辑层。 该层包含存取模型及调取恰当模板的相关逻辑，可以把它看作模型与模板之间的桥梁。

Django 处理请求的流程：

{% asset_img flow.png Django 请求处理流程 %}

request 进来-->`urls.py`根据 url 指配处理函数-->`views.py`处理 request 请求-->返回 response


<!--more-->


# URL 调度器 urls.py

编写`urls.py`将 request 请求传递给`views.py`相应的函数处理

```python
from django.conf.urls import url
from django.contrib import admin
from blogapp import views

urlpatterns = [
    url(r'^admin/', admin.site.urls),  # 匹配传递给管理页面函数
    url(r'^$', views.home),  # 匹配传递给 home 视图函数
]
```

# Views 视图函数 views.py

编写`views.py`处理由`urls.py`转来的 request 请求，home 视图函数处理完后返回 response

```python
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def home(request):
    return HttpResponse("Hello World, Django")
```

# 模板Templates

为了使返回的 response 达到更好的显示效果，可以让视图函数加载模板处理后再返回。

在`setting.py`的 `TEMPLATES` 设置模板文件的存放路径

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],  # 设置模板文件路径
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

相应的也要修改 `views.py`中的视图函数，将`request 请求`、`模板文件`和`参数`传入传递函数`render`渲染后返回

```python
from django.shortcuts import render
from django.http import HttpResponse
from blogapp.models import Article

# Create your views here.
def home(request):
    posts_list = Article.objects.all()
    return render(request, 'home.html', {'post_list' : post_list})
```

在`templates`文件夹编写`home.html`文件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

    {% for post in post_list %}
        标题：<a href="{{ post.id }}">{{ post.title }}</a>
        发布时间： <a class="post-author" href="">{{ post.publish_time |date:"Y/m/d"}}</a>
        分类： <a class="post-category post-category-yui" href="/categories/">{{ post.category }}</a>
        正文： <p>{{ post.content }}</p>
    {% endfor %}

</body>
</html>
```