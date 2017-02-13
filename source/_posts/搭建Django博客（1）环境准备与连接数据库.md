---
title: 搭建Django博客（1）环境准备与连接数据库
date: 2016-09-05 18:46:00
categories: 
- Django
tags:
- Django
---

# 环境准备

> 因为我使用的是 Windows 系统，下面所有的操作都是在 Windows 系统下进行的，大部分操作和 Linux、macOS 相似，但有些地方要注意区分。

使用 CMD 命令行创建 Python 虚拟环境，并在虚拟环境中安装 Django

```powershell
$ virtualenv [虚拟环境名]
$ pip install django
```


<!--more-->


- 在 PyCharm 中创建 Django 项目，并选择刚刚创建的虚拟环境为其解释器，完成后将在指定目录生成 Django 基本目录及文件。
- 当然，也可以使用 CMD 命令行创建 Django 项目（我创建的项目名是：myblog）：

```powershell
$ 虚拟环境目录\Scripts\django-admin startproject [项目名]
$ 虚拟环境目录\Scripts\django-admin startproject myblog
```

运行 Django 项目：

```powershell
$ python manage.py runserver
```

打开 http://127.0.0.1:8000/ 页面出现如下页面表示 Django 安装成功

{% asset_img WelcomeToDjango.png Welcome to Django %}

创建 Django 项目内的应用（我创建的应用名：blogapp）：

```powershell
$ python manage.py startapp [应用名]
$ python manage.py startapp blogapp
```

# 安装数据库

Django 官方支持 PostgreSQL、MySQL、SQLite和Oracle 数据库，还有许多第三方提供的数据库也支持在 Django 中使用：SAP SQL Anywhere、IBM DB2、Microsoft SQL Server、Firebird、ODBC

为了方便和在网上能够找到更多资源，我直接选择了官方支持的 PostgreSQL 数据库，下面开始安装：

1. 登陆 PostgreSQL 官网下载数据库直接安装

   https://www.postgresql.org/

2. 安装 psycopg2 扩展让 Django 连接 PostgreSQL 数据库：

   http://initd.org/psycopg/

   CMD 安装 psycopg2 命令：

```powershell
$ easy_install [安装包名]
例如：
$ easy_install psycopg2-2.6.2.win32-py3.4-pg9.5.3-release.exe
```

*注意：Python 32/64位要对应下载 psycopg2 32/64位程序，否则 Django 连接数据库时会报错；但 PostgreSQL 安装32/64位都没问题*

# **连接数据库**

要连接数据库，当然要先新建一个数据库，直接在 PostgreSQL 管理工具里新建即可，这里数据库取名 `blog`，所有者为 `postgres`

编辑 Django 项目的 `setting.py` 文件中的 `DATABASES`字段，填入数据库信息：

```powershell
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql', #PostgreSQL数据库规定写法
        'NAME': 'blog', #数据库名称
        'USER': 'postgres', #数据库用户
        'PASSWORD': '123', #数据库密码
        'HOST': '127.0.0.1', #数据库所在的IP地址
        'PORT': '5432', #连接数据库的端口号
    }
}
```

到这里就成功连接数据库了！