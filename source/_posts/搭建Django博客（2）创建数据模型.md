---
title: 搭建Django博客（2）创建数据模型
date: 2016-09-05 19:46:09
categories: Django
tags:
---

前面我们创建并连接了数据库，但我们没有向数据库写入任何数据，在 Django 中直接与数据库交互的是数据模型，可以通过数据模型实现对数据库的各种操作。

# 创建博客文章的数据模型

Django 中用`models.py`文件中的一个类表示数据库中的一张表，类中的属性表示数据库中的一列。因此，我们可以编辑`models.py`文件来创建数据库表。

现在我们来创建一个`Article`类，类中的`title` `category` `publish_time` `content`属性分别对应数据库中的一个列。

```python
from django.db import models

# Create your models here.

class Article(models.Model):
    title = models.CharField('标题',max_length=50)
    category = models.CharField('分类',max_length=50, blank=True)
    publish_time = models.DateField('发布时间',auto_now_add=True)
    content = models.TextField('正文')
    # 下面2个属性我觉得没啥必要就注释掉了，以后如果有需要再启用
    # modify_date = models.DateField('修改日期',auto_now=True)
    # author = models.CharField('作者',max_length=50)

    class Meta:
        db_table = 'article'  #数据库表名
        ordering = ['-id']  # 按创建时分配的 id 倒序排列

    def __str__(self):
        return self.title
```

# 将博客应用注册到 Django

编辑`setting.py`文件中的`INSTALLED_APPS`字段，加入应用名称；将创建的博客应用`blogapp`注册到 Django ，使应用的数据模型写入数据库。

```python
INSTALLED_APPS = [
    'blogapp',  # 创建的 Django 应用 blogapp
    'django.contrib.admin',  # Django 默认自带的应用
    'django.contrib.auth',   # Django 默认自带的应用
    'django.contrib.contenttypes',   # Django 默认自带的应用
    'django.contrib.sessions',   # Django 默认自带的应用
    'django.contrib.messages',   # Django 默认自带的应用
    'django.contrib.staticfiles',   # Django 默认自带的应用
]
```

现在我们已经准备好将自定义的数据模型写入数据库：

```powershell
$ python manage.py makemigratinons
```

*注意：执行上面命令后，会在`myblog/blogapp/migrations`文件夹下创建一个 python 迁移文件，但数据模型此时并未写入数据库*

真正更新数据库的命令

```powershell
$ python manage.py migrate
```
# 登录 Django 后台管理页面

我们已经在数据库中创建了表和列用来保存我们的数据，那我们怎么把各个列的数据写入数据库呢？显然，用 SQL 语句是可行的，但是，估计用不了多久你就会崩溃掉（微笑脸）

贴心的 Django 已经为我们准备好易于操作的后台管理页面了，只需创建一个后台管理用户：

```powershell
$ python manage.py createsuperuser
```

创建用户后，打开[http://127.0.0.1:8000/admin/](http://127.0.0.1:8000/admin/)，使用刚刚创建的用户名和密码登录，看到下面的界面就表示成功了！

{% asset_img login.png Login page %}

{% asset_img admin.png Admin page %}