---
title: 搭建Django博客（3）网页处理逻辑
date: 2016-09-05 21:18:32
categories: Django
tags:
- Django
---

# 网页程序的逻辑

先看一下 Django 处理请求的流程

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