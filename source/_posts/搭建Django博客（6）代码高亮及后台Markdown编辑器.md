---
title: 搭建Django博客（6）代码高亮及后台Markdown编辑器
date: 2016-09-05 21:32:17
categories: Django
tags:
- Django
---



# 代码高亮

安装语法高亮库 Pygments

```powershell
$ pip install Pygments
```

<!--more-->


将 pygments 的 css 主题文件放到七牛云，可享受 CDN 加速，并在 base.html 的`<head>` `</head>`之间加入`<link rel="stylesheet" href="http://picturebag.qiniudn.com/monokai.css">`

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="description" content="A layout example that shows off a blog page with a list of posts.">

    <title>WISH007 Blog</title>
    <link rel="stylesheet" href="http://yui.yahooapis.com/pure/0.5.0/pure-min.css">
    <link rel="stylesheet" href="http://yui.yahooapis.com/pure/0.5.0/grids-responsive-min.css">
    <link rel="stylesheet" href="http://picturebag.qiniudn.com/blog.css">
    <link rel="stylesheet" href="http://picturebag.qiniudn.com/monokai.css">
</head>
```

# 后台 Markdown 编辑器

安装`django-pagedown`编辑器

```powershell
pip3 install django-pagedown
```
在`setting.py`的`INSTALLED_APPS`添加`pagedown`
```python
INSTALLED_APPS = [
    'blogapp',
    'pagedown',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

编辑`admin.py`

```python
from django.contrib import admin
from blogapp.models import Article
from pagedown.widgets import AdminPagedownWidget
from django import forms

# Define your form here.
class ArticleForm(forms.ModelForm):
    content = forms.CharField(widget=AdminPagedownWidget())

    class Meta:
        model = Article
        fields = '__all__'

class ArticleAdmin(admin.ModelAdmin):
    form = ArticleForm

# Register your models here.
admin.site.register(Article,ArticleAdmin)
```