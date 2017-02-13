---
title: 搭建Django博客（8）部署Django博客到VPS
categories:
  - Django
tags:
  - Django
  - VPS
  - Linux
  - Nginx
  - WSGI
  - uWSGI
  - supervisor
date: 2017-02-02 12:08:20
---





本项目采用三层架构部署，Nginx 作为反向代理服务器，WSGI 层使用 [uWSGI](http://uwsgi-docs-cn.readthedocs.io/zh_CN/latest/WSGIquickstart.html)，Web 应用为 Django；同时，使用  Supervisor 进程守护工具守护 uWSGI 进程。

{% asset_img deploy.png Nginx + uWSGI + Django + Supervisor %}


# 项目地址

[GitHub - wish007/django_blog](https://github.com/wish007/django_blog)

<!--more-->


# 准备工作

在开发阶段，调试模式处于打开状态可以方便我们调试，但部署到生产环境显然不能这么做，必须对 Django 项目设置文件`settings.py`做些修改，提升系统安全性：

```python
SECRET_KEY = 'This is a secret key!'  -->
SECRET_KEY = 'sqDZl^NeQ7SB1wJqeDoxhpf8RnTA1hMhJd5NQApcLizIx*!GRe'  
# 此项修改尤为重要，自行生成一串足够长和复杂的 KEY，如果你把项目代码放到 GitHub，也记得要在生产环境修改此字段

DEBUG = True --> DEBUG = False  #关闭调试模式
ALLOWED_HOSTS = [] --> ALLOWED_HOSTS = ['*']  #允许所有IP访问，否则他人无法访问
```

开发阶段，调试模式开启时 Django 会自动找到项目的静态文件来响应请求，关闭后 Django 将不处理对静态文件的请求，对这些请求返回 404 错误；部署到线上时，Nginx 会处理对静态文件的请求，所以必须将项目涉及到的静态文件搜集起来。在`settings.py`文件中添加`STATIC_ROOT`字段，设置搜集起来的静态文件的保存目录：

```python
STATIC_ROOT = os.path.join(BASE_DIR, "static")
```

执行搜集静态文件的命令：

```bash
python manage.py collectstatic
```

在项目根目录下将生产一个`static`文件夹，里面保存了项目用到的所有静态文件。

创建运行环境依赖文件`requirements.txt`:

```bash
pip freeze > requirements.txt
```



# 正式部署



## 创建运行环境

首先，将项目所有的文件导入服务器用户目录 /home/sun/django_blog，创建运行环境：

```bash
cd /home/sun/django_blog
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
```



##  创建数据库

原项目里的数据库文件可以保留，跳过这一步也没问题；如果不保留原有数据库，可以按下面步骤重新创建数据库并导入数据结构：

生成数据库文件：

```bash
python manage.py runserver 
```

生成数据结构迁移文件：

```bash
python manage.py makemigrations
```

将数据结构写入数据库：

```bash
python manage.py migrate
```

创建管理员账号：

```bash
python manage.py createsuperuser
```

运行 Django：

```bash
cd /home/sun/django_blog
source venv/bin/activate
python manage.py runserver
```

测试输出是否正常：

```bash
curl http://127.0.0.1:8000
```



##  配置uWSGI

安装 uWSGI

```bash
pip install uwsgi
```

配置 uWSGI

> 在 Django 项目目录下新建 uWSGI 配置文件 uwsgi_config.ini 并输入

```bash
[uwsgi]

# Django-related settings
chdir = /home/sun/django_blog
home = /home/sun/django_blog/venv
module = myblog.wsgi

# process-related settings
master = true
socket = 127.0.0.1:8000
processes = 4
threads = 2
buffer-size = 32768
```

启动 uWSGI

```bash
(venv) [sun@SS django_blog]$ uwsgi uwsgi_config.ini
```



## 配置Nginx

安装 Nginx

```bash
yum install nginx
```

配置 Nginx

> 配置文件目录：/etc/nginx/conf.d 目录下的 default.conf
>
> 即使已经有监听同一个端口的应用，也可以直接添加另一个应用，因为他们的`server_name`不同，Nginx 会自动处理好不同站点的转发。

```bash
server { 
  listen 80; 
  server_name xx.xx.xx.xx; #公网地址 
  location / { 
  include uwsgi_params;
  uwsgi_pass 127.0.0.1:8000; # 指向uwsgi 所应用的内部地址,所有请求将转发给uwsgi 处理 
  uwsgi_param UWSGI_PYHOME /home/sun/env/django_blog; # 指向虚拟环境目录 
  uwsgi_param UWSGI_CHDIR /home/sun/django_blog; # 指向网站根目录 
  uwsgi_param UWSGI_SCRIPT manage:app; # 指定启动程序
  uwsgi_read_timeout 100; 
 }
  location /static/ {  #转发对静态文件的请求
  root /home/sun/django_blog;  #必须填 static 文件夹所在目录
}
}
```

启动 Nginx：

```bash
service nginx start
```

Nginx 相关命令：

```bash
# 关闭 Nginx
service nginx stop

# 重启 Nginx
service nginx restart
```



## 配置Supervisor

> [Supervisor](http://supervisord.org/) 有两个主要的组成部分：
>
> 1. supervisord
>
>    它是 Supervisor 的服务端，运行 Supervisor 时会启动一个 supervisord 进程，负责启动所管理的进程，并将所管理的进程作为自己的子进程来启动，将普通的命令行进程变为后台daemon，响应客户端命令、监控进程状态、自动重启 crashed 掉的子进程、记录子进程的 stdout、stderr 等。
>
> 2. supervisorctl
>
>    它是 Supervisor 的客户端，提供了 shell-like 接口来调用 supervisord，通过 supervisorctl 用户可以连接到多个 supervisord 进程，获取supervisord 所管理的子进程状态，可以执行 stop、start、restart 等命令来管理这些子进程。

安装完 uWSGI、Nginx 后，Nginx 可以直接设置开机启动，但 uWSGI 还不能直接开机启动，这里我用 Supervisor 来启动并守护  uWSGI 运行，在全局 Python 环境安装 Supervisor：

```bash
pip install supervisor
```

配置 Supervisor：

> 配置文件目录：/etc/supervisord.conf

```bash
[program:django_blog]
# 启动命令入口
command=/home/sun/env/django_blog/bin/uwsgi /home/sun/django_blog/uwsgi_config.ini
# 命令程序所在目录
directory=/home/sun/django_blog
user=sun
autostart=true
autorestart=true
startsecs=10
startretries=10
#日志地址
stdout_logfile=/home/sun/django_blog/uwsgi_supervisor.log
stdout_logfile_maxbytes = 50MB
stderr_logfile=/home/sun/django_blog/uwsgi_err.log
stderr_logfile_maxbytes = 50MB
```

设置 supervisord 开机启动

```bash
chkconfig --add supervisord
chkconfig supervisord on
```
启动 supervisord

```bash
service supervisord start
```
查看 django_blog 进程是否运行

```bash
supervisorctl status
# 输出 RUNNING 说明子进程成功运行
django_blog    RUNNING   pid 17168, uptime 0:03:25
```



supervisord 相关命令

```bash
# 重启 supervisord 进程
service supervisord restart

# 停止 supervisord 进程
service supervisord stop

# 注意：supervisord 重启或停止后，uwsgi 进程会不受 supervisord 控制，必须手动 kill 掉 uwsgi 进程后再启动 supervisord 进程
```

supervisorctl 相关命令

```bash
# 启动某个进程，program_name 为 [program:x] 里的 x
supervisorctl start program_name
# 停止某个进程
supervisorctl stop program_name
# 重启某个进程
supervisorctl restart program_name

# 结束所有属于名为 groupworker 这个分组的进程 (start，restart 同理)
supervisorctl stop groupworker:
# 结束 groupworker:name1 这个进程 (start，restart 同理)
supervisorctl stop groupworker:name1
# 停止全部进程，注意：start、restart、stop 都不会载入最新的配置文件
supervisorctl stop all

# 停止所有进程并按新的配置启动所有进程
supervisorctl reload
# 重启配置有改动的进程，启动配置中新加入的进程，配置没有改动的进程会保持原有的启动或停止状态
supervisorctl update
```





# Django静态文件处理

根据上面的步骤，一般能成功运行起来 Django，主页面的访问也没问题。

当打开 Django 管理页面 `公网IP:端口/admin`，发现登录页面的样式丢失了，查看网页源代码，发现点击静态文件会产生 403 错误的返回，说明服务器收到了请求但拒绝处理请求，信息是 Nginx 服务器返回的，接着想到查看 Nginx 日志，打开 /var/log/nginx/error.log 文件，发现有这样的错误：

```bash
2017/02/03 13:05:27 [error] 31497#0: *1 open() "/home/sun/django_blog/static/admin/css/login.css" failed (13: Permission denied), client: 12.56.11.158, server: xx.xx.xx.xx, request: "GET /static/admin/css/login.css HTTP/1.1", host: "xx.xx.xx.xx"
```

说明打开静态文件失败是由于`Permission denied`产生的，很可能与 Nginx 进程的权限和静态文件的权限有关，打开 Nginx 配置文件 /etc/nginx/nginx.conf ：

```bash
user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /var/run/nginx.pid;
```

发现 Nginx worker process 是以 nginx 用户启动的，将 worker 启动用户改为 django_blog/static/ 目录所有者用户相同：

```bash
user nginx --> user sun
```

重启 Nginx 进程

```bash
service nginx restart
```

Django 管理页面的样式恢复了。



参考：[nginx+django+uwsgi static files 403 Forbidden](http://stackoverflow.com/questions/28732692/nginxdjangouwsgi-static-files-403-forbidden)





# 进阶阅读

[利用 NGINX 最大化 Python 性能  第一部分：Web 服务和缓存](http://www.ituring.com.cn/article/214859)

[利用 NGINX 最大化 Python 性能  第二部分：负载均衡和监控](http://www.ituring.com.cn/article/215554)