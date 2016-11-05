---
title: 模拟登陆无线路由器（TL-WDR5600）
date: 2016-11-02 22:16:28
categories: Python
tags:
- Python
- 爬虫
- 无线路由器
---

# Why

最近对模拟登录很感兴趣，因为很多事情都需要登录了才能做，利用程序实现自动化能做到许多十分有趣的事情。

我已经打手头上这台 TP-LINK 的 WDR5600 主意很久了，趁着最近在学习 HTTP ，正好实现以前的一些想法。

# GitHub Repository

[GitHub - wish007/router](https://github.com/wish007/router)

# Library

- [Requests](http://docs.python-requests.org/zh_CN/latest/) ：HTTP for Humans ，涉及到 HTTP 怎能少了它
- [json](https://docs.python.org/3.5/library/json.html)：JSON 数据格式的编码和解码
- [urllib.parse](https://docs.python.org/3.5/library/urllib.parse.html)：对 URL 编码进行解码
- Chrome开发者工具：调试 HTTP 请求、JavaScript 的利器

<!--more-->

# How

既然无线路由器是通过 Web 页面登录的，那直接打开登录页面用Chrome开发者工具获取网络通信过程。

- 输入 http://192.168.1.1 打开登录页面，现在有些无线路由器只需要密码就可以登录了，我手头上的就是这种：

  {% asset_img 登录界面.png %}

  ​

  输入密码`administrator`点登录开始抓包：

  {% asset_img 登录.png %}

  ​

  {% asset_img stok.png %}

  ​

  在 Request Payload 可以很明显看到`{"password":"WaQ7xPr3L41vMwK"}`，Response 返回`{ "stok": "b686b22c7a3f1c1fb983b0e30aa98fae", "error_code": 0 }`，`password`应该就是我们要找密码字段，返回的`stok`相当于后续通信的`token`口令，`error_code`的值为`0`表示认证通过。

- 但是请求的`{"password":"WaQ7xPr3L41vMwK"}`和真正的密码`administrator`完全不同，应该是对密码进行了加密处理，打开登录界面时加载了许多 JavaScript 文件，猜测可能是通过 JavaScript 来加密，切换到 Chrome 开发者工具的 Soures 标签页，尝试搜索`password`、`pwd`等密码相关的关键字，多试几次就可以找到加密代码，实在不行就设置断点单步查看登录涉及的代码。

  设置断点后在`orgAuthPwd`函数发现传入的`pwd`参数值为真实的密码`administrator`，最后返回一个带参数的`securityEncode`函数，继续运行可以看到变量`output`的值在一步步变为登录请求的`{"password":"WaQ7xPr3L41vMwK"}`

  {% asset_img 加密函数1.png %}

  ​

  {% asset_img 加密函数2.png %}

  ​

  既然找到了加密代码，那就马上用 Python 重写：

  ```python
  def encrypt_pwd(password):
      input1 = "RDpbLfCPsJZ7fiv"
      input3 = "yLwVl0zKqws7LgKPRQ84Mdt708T1qQ3Ha7xv3H7NyU84p21BriUWBU43odz3iP4rBL3cD02KZciXTysVXiV8ngg6vL48rPJyAUw0HurW20xqxv9aYb4M9wK1Ae0wlro510qXeU07kV57fQMc8L6aLgMLwygtc0F10a0Dg70TOoouyFhdysuRMO51yY5ZlOZZLEal1h0t9YQW0Ko7oBwmCAHoic4HYbUyVeU3sfQ1xtXcPcf1aT303wAQhv66qzW"
      len1 = len(input1)
      len2 = len(password)
      dictionary = input3
      lenDict = len(dictionary)
      output = ''
      if len1 > len2:
          length = len1
      else:
          length = len2
      index = 0
      while index < length:
          # 十六进制数 0xBB 的十进制为 187
          cl = 187
          cr = 187
          if index >= len1:
              # ord() 函数返回字符的整数表示
              cr = ord(password[index])
          elif index >= len2:
              cl = ord(input1[index])
          else:
              cl = ord(input1[index])
              cr = ord(password[index])
          index += 1
          # chr() 函数返回整数对应的字符
          output = output + chr(ord(dictionary[cl ^ cr]) % lenDict)
      return output
  ```

- 有了加密后的密码，顺理成章地将它作为 Payload 发送请求，返回的响应为 JSON 格式数据，通过 json 库解析获得 token：

  ```python
  encryptPwd = '加密后的密码'
  url = 'http://192.168.1.1/'
  headers = {'Content-Type': 'application/json; charset=UTF-8'}
  payload = '{"method":"do","login":{"password":"%s"}}' % encryptPwd
  response = requests.post(url, data=payload, headers=headers)
  stok = json.loads(response.text)['stok']
  ```

- 获得 stok 后就可以将它加入请求 URL 中获取数据了；例如获取用户当前的上传速度、下载速度、限速信息等。返回的信息为 JSON 格式数据，可以解析做格式化处理增强可读性。

  {% asset_img 获取数据.png %}

  ```python
  stok = json.loads(response.text)['stok']
  headers = {'Content-Type': 'application/json; charset=UTF-8'}
  url = '%sstok=%s/ds' % ('http://192.168.1.1/',stok)
  payload = '{"hosts_info":{"table":"host_info"},"method":"get"}'
  response = requests.post(url, data=payload, headers=headers)
  ```

  # More

  根据上面的分析，发现整个过程的关键点有几个：

  - 请求参数
  - 密码加密函数
  - 获取 stok

  获取到 stok 可以做的事情就很多了，可以更改请求参数（具体参数要提前抓取）获取数据、操作路由器，有兴趣的可以根据我的代码自行拓展。

  ​

