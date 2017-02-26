---
title: 用Requests和BeautifulSoup爬取妹子图
date: 2016-09-10 16:58:47
categories: Python
tags:
- Python
- Requests
- BeautifulSoup
- 爬虫
---

# 前言

其实这个爬虫程序已经写好大半年了，中途有做过一些修改，现在觉得有必要记录一下过程。

说到爬虫，大多为了批量爬取各种信息，但我写这个爬虫的初衷倒不是为了妹子图片（虽然真的下载了不少妹子图片⁄(⁄ ⁄•⁄ω⁄•⁄ ⁄)⁄，更多是为了练习 Python 算法和常用库，顺带还了解了 HTTP 请求的一些细节。

# 工具介绍

- [Requests](http://docs.python-requests.org/zh_CN/latest/) ：用来发送 HTTP 请求，获取 HTTP 响应
- [Beautiful Soup](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/) ：从获取到的 HTML 文件中提取数据
- Chrome开发者工具：查看 HTTP 通信过程，读取 HTML 源码


<!--more-->


# 爬虫程序分析

> 本爬虫程序的目标是爬取[妹子图(www.meizitu.com)](http://www.meizitu.com/)网站上的妹子图片，并按标题分类保存到本地计算机

## GitHub 项目地址：

[GitHub - wish007/crawler](https://github.com/wish007/crawler)

爬取结果

{% asset_img meizitu.png %}

## 爬虫代码：

程序包含直接下载图片到本地的版本和保存图片链接到 SQLite 数据库的版本，后者已经使用面向对象的编程方法重写，重写后结构更加清晰，可以点击上面的项目地址查看。

**重写后：**

```python
import requests
from bs4 import BeautifulSoup
import sqlite3

header = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.110 Safari/537.36'
          }

class Meizitu():

    def __init__(self, start, end):
        self.start = start
        self.end = end

    def url(self):
        while self.start < self.end:
            yield 'http://www.meizitu.com/a/%s.html' % self.start
            self.start +=1

    def picture(self, url):
        r = requests.get(url, headers=header, timeout=30)
        r.encoding = 'gb2312'
        soup = BeautifulSoup(r.text, "html.parser")
        try:
            title = soup.title.string[:-6]
            for links in soup.find_all(class_='postContent'):
                for l in links.find_all('img'):
                    link = l.get('src')
                    yield title, link
        except TypeError:
            title = 'Empty'
            link = 'Empty'
            return title, link

    def save(self, url, title, link):
        cur.execute("INSERT INTO meizitu VALUES ('%s','%s','%s')" % (url,title,link))
        print(url + ' 已写入数据库')

if __name__ == '__main__':
    mzt = Meizitu(500, 520)
    conn = sqlite3.connect('meizitu.db')
    cur = conn.cursor()
    cur.execute('CREATE TABLE IF NOT EXISTS meizitu (page TEXT, title TEXT, url TEXT)')
    try:
        for url in mzt.url():
            for title, link in mzt.picture(url):
                mzt.save(url, title, link)
    except KeyboardInterrupt:
        cur.close()
        conn.commit()
        conn.close()
```



### meizitu.py 分为 5个函数

- collect_url()
- collect_picture_link()
- create_directory()
- download_picture()
- run()

#### collect_url()

> 根据妹子图网站页面的 URL 规律，设置需要爬取的起止 URL，最后返回需要将要爬取的 URL 列表

```python
def collect_url():
    """创建需要下载图片的网页URL"""

    url_list = []
    # 起始网页
    start = 5420
    # 结束网页
    end = 5425
    while start <= end:
        url = 'http://www.meizitu.com/a/' + str(start) + '.html'
        url_list.append(url)
        start = start + 1
    return url_list
```

#### collect_picture_link()

> 使用  Beautiful Soup 的`find_all`方法搜索 HTML 文档树，返回图片的 URL 列表

```python
def collect_picture_link(soup):
    """将网页URL中的图片URL抓取出来"""

    picture_link_list = []
    for link_node in soup.find_all(id='picture'):
        for link in link_node.find_all('img'):
            picture_link_list.append(link.get('src'))
    return picture_link_list
```

#### create_directory()

> 创建以网页标题为名称的文件夹来存放图片

```python
def create_directory(url, soup):
    """创建以网页标题为名称的文件夹"""

    title = soup.title.string[:-6]
    url_cut = len(url) - 5
    url_id = url[25:url_cut]
    # Python文件的绝对路径
    path = os.path.dirname(os.path.realpath(__file__))
    if os.path.exists(path + '/' + url_id + ' ' + title):
        None
    else:
        os.mkdir(path + '/' + url_id + ' ' + title)
    dir = path + '/' + url_id + ' ' + title + '/'
    return dir
```

#### download_picture()

> 将图片保存到以网页标题为名称的文件夹

```python
def download_picture(links, dir, header):
    """下载并保存图片"""

    i = 1
    for picture_link in links:
        picture_name = picture_link[-18:].replace('/','-')
        if os.path.exists(dir + picture_name):
            None
        else:
            picture = requests.get(picture_link, headers=header, timeout=50)
            with open(dir + picture_name, 'wb') as file:
                file.write(picture.content)
                print('第 ' + str(i) + ' 张完成')
                i = i + 1
```

#### run()

> 程序的主函数。
>
> 使用 Requests 库的`get`方法发送请求，并用 BeautifulSoup 库和解析器解析网页的响应。

```python
def run():
    # 加入headers模拟浏览器请求
    header = {'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.110 Safari/537.36',
              }
    url_list = collect_url()
    for url in url_list:
        # 加入timeout超时时间
        response = requests.get(url, headers=header, timeout=30)
        # 将返回的网页编码强制设定为‘gb2312’，防止request将返回解析为其他编码
        response.encoding = 'gb2312'
        # BeautifulSoup的第2个参数是解析器，解析器主要有：自带的Python标准库解析器html.parser、lxml、html5lib
        soup = BeautifulSoup(response.text, "html.parser")
        links = collect_picture_link(soup)
        dir = create_directory(url, soup)
        download_picture(links, dir, header)
```



## 保存到 SQLite

> 对于练习 Python 算法来说，其实没有必要真正把图片下载下来，毕竟下载图片比较费时间。倒是可以先把图片的 URL 等信息保存到数据库，将来可以用多线程下载。
>
> 将`create_directory()`函数替换为`database()`，去掉`download_picture()`函数即可。

###  database()

> 将网页 URL、网页标题、图片 URL 保存到 SQLite 数据库

```python
def database(url,title,link):
    """将网页URL、网页标题、网页中的图片URL保存到SQLite数据库"""

    # 连接数据库
    conn = sqlite3.connect('picture_url.db')
    cur = conn.cursor()
    # 创建一个表
    cur.execute('CREATE TABLE IF NOT EXISTS picture (page TEXT, title TEXT, url TEXT)')
    # 插入数据
    cur.execute("INSERT INTO picture VALUES ('%s','%s','%s')" % (url,title,link))
    cur.close()
    conn.commit()
    conn.close()
```



# To Do

- 多线程爬取网页、多线程下载图片（加快下载速度）
- 加入 gzip 压缩格式支持（加快下载速度）
- 增加代理 IP 地址池（防止 IP 被禁用）