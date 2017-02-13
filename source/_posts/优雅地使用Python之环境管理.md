---
title: 优雅地使用Python之环境管理
categories:
  - Python
tags:
  - virtualenv
  - virtualenvwrapper
date: 2016-09-04 21:34:53
---



# virtualenv

virtualenv 用于创建独立的 Python 环境，多个Python相互独立，互不影响，它能够：
1. 在没有权限的情况下安装新套件
2. 隔离项目之间的第三方包依赖
3. 方便部署到生产环境



**安装方法**

```bash
pip install virtualenv
```

<!--more-->


**使用**

- 创建虚拟环境

  ```bash
  virtualenv  虚拟环境名称
  ```

- 默认情况下，虚拟环境会依赖系统环境中的 site packages，就是说系统中已经安装好的第三方 package 也会安装在虚拟环境中，如果不想依赖这些 package ，那么可以加上参数 --no-site-packages 建立虚拟环境

  ```bash
  virtualenv --no-site-packages 虚拟环境名称
  ```

- 激活虚拟环境

  Linux

  ```bash
  source path/to/virtualenv/bin/activate
  ```

  Windows

  ```bash
  path\to\virtualenv\Scripts\activate
  ```

- 退出虚拟环境

  ```bash
  deactivate
  ```







# virtualenvwrapper

Virtualenvwrapper 是 Virtualenv 的一个扩展，可使虚拟环境的管理变得更容易，Virtualenvwrapper 提供以下功能：

1. 将所有的虚拟环境整合在一个目录下
2. 管理（新增、移除、复制）所有的虚拟环境
3. 可以使用一个命令切换虚拟环境
4. Tab 补全虚拟环境的名字
5. 每个操作都提供允许使用者自定的 hooks
6. 可撰写容易分享的 extension plugin 系统



**安装**

Linux

```bash
pip install virtualenvwrapper
```

Windows

```bash
pip install virtualenvwrapper-win
```



**使用**

列出虚拟环境列表

```bash
workon
# 或者
lsvirtualenv
```

新建虚拟环境

```bash
mkvirtualenv 虚拟环境名称
```

启动 / 切换虚拟环境

```bash
workon 虚拟环境名称
```

删除虚拟环境

```bash
rmvirtualenv 虚拟环境名称
```

离开虚拟环境

```bash
deactivate
```



**自定义新建虚拟环境的保存目录**

Linux

修改~/.bash_profile或其它环境变量相关文件，添加以下语句

```bash
export WORKON_HOME=$HOME/.virtualenvs
```

Windows

Windows下默认虚拟环境是放在用户名下面的 Envs 中的，与桌面、我的文档、下载等文件夹放在一起。更改方法：计算机-->属性-->高级系统设置-->环境变量-->添加`WORKON_HOME`





# 小结

使用 virtualenv + virtualenvwrapper 可以很好的完成环境隔离，保证对每个应用的环境是干净的，可以通过以下 2条命令导出包依赖或安装包依赖：

**导出包依赖**

```bash
pip freeze > requirements.txt
```

**安装包依赖**

```bash
pip install -r requirements.txt
```