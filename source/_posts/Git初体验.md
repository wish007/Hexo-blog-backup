---
title: Git初体验
date: 2016-09-10 01:25:42
categories: Git
tags:
- Git
---

# 安装Git

从 [http://git-scm.com/downloads](http://git-scm.com/downloads) 下载安装 Git 客户端


# 连接GitHub

> 为了将本地 Git 仓库推送到 GitHub 远程仓库，需要设置通过 SSH 加密进行数据传输

## 设置机器名

Git 是分布式版本控制系统，每个机器都必须自报家门：`你的名字`和 `Email 地址 `

使用 GitHub 作为远程仓库，最好填上你的 GitHub 用户名和 GitHub 的注册邮箱，否则可能无法在 GitHub 中记录你的 Contribution

```bash
$ git config --global user.name "Your GitHub Name"
$ git config --global user.email "email@example.com"
```

> 注意：`gitconfig`命令的`--global`参数，表示你这台机器上所有的 Git 仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

<!--more-->

## 创建SSH Key

配置完Git后，在用户主目录下会生成`.ssh`文件夹，里面有`id_rsa`私钥和`id_rsa.pub`公钥这两个SSH Key的密钥对，如果没有：

```bash
$ ssh-keygen -t rsa -C "youremail@example.com"
```

## 添加SSH Key到GitHub

登陆GitHub，打开`Account settings`，选择`SSH and GPG keys`页面；点`New SSH Key`，填上任意`Title`，在`Key`文本框里粘贴`id_rsa.pub`文件的内容

{% asset_img addsshkey.png Add SSH Key to GitHub %}

> **为什么GitHub需要SSH Key呢？**
>
> 因为GitHub需要识别推送的提交确实是你推送的，Git也支持SSH协议；所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。
>
> 当然，GitHub允许你添加多个Key。在不同电脑上，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上向GitHub推送。



# Git常用命令

## Git 区域和命令间的关系

{% asset_img git.jpg %}

> 工作区：workspace
>
> 暂存区：Index（也叫 stage）
>
> 版本库：Index + HEAD（Repository，包含 master 在内的各个分支）

## 初始化Git仓库

```bash
$ git init
```

> 创建仓库后，目录下会多一个`.git`目录，用来跟踪管理版本库，不能修改，`.git`目录是隐藏的
>

## 添加文件到暂存区

```bash
$ git add [File Name]
$ git add .  # 添加所有文件到暂存区
```

## 提交到当前分支

```bash
$ git commit -m "代码提交信息"
```

## 本地仓库关联远程仓库

```bash
$ git remote add origin git@github.com:[GitHub用户名]/远程仓库名.git
```

## 本地仓库推送到远程仓库

```bash
$ git push origin master  # master也可以换成其他分支
```

## 克隆远程仓库到本地

```bash
$ git clone git@github.com:[GitHub用户名]/远程仓库名.git
```

## 分支类命令

### 创建分支

```bash
$ git branch [分支名]
$ git checkout -b [分支名]   # 创建并切换到分支
```

### 切换分支

```bash
$ git checkout [分支名]
```

### 删除分支

```bash
$ git branch -d [分支名]
```

### 查询分支

```bash
$ git branch  # 当前分支前面会有一个*号
```

### 合并其他分支到当前分支

```bash
$ git merge [其他分支名]
```

### 取回远程仓库并与本地分支合并

```bash
$ git pull origin [本地分支名]  # git pull = git fetch + git merge
```

## 查询类命令

### 发生更改的文件

```bash
$ git status
```

### 工作区和暂存区的不同

```bash
$ git diff
```

### 提交日志

```bash
$ git log
```

### 提交ID和提交信息

```bash
$ git reflog
```
