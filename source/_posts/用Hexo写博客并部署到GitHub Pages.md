---
title: 用Hexo写博客并部署到GitHub Pages
date: 2016-09-03 22:50:33
categories: Hexo
tags:
- Hexo
---

# Hexo常用命令

## 创建新的 Hexo 网站

```bash
hexo init [folder]
或在当前目录下创建
hexo init
```

## 发表新文章

``` bash
hexo new "文章标题"
或
hexo n "文章标题"
```
执行命令后会在 `Hexo/source/_posts` 文件夹生成 `文章标题.md` 的文件，再用编辑器打开编写；当然也可以直接在该目录创建 Markdown 文件。


<!--more-->

## 生成 Hexo 博客静态文件

``` bash
hexo generate
或
hexo g
```

## 打开 Hexo 本地服务器

``` bash
hexo server
或
hexo s
```

默认情况下，访问网址：http://localhost:4000/ ，即可在查看本地效果。

# 将博客部署到 GitHub

部署前要编辑 Hexo 站点配置文件 `Hexo/_config.yml`，这样 Hexo 才知道要将你的站点同步到哪里；下面的`repository:`后面记得替换成自己的 GitHub 仓库。

```bash
deploy:
  type: git
  repository: https://github.com/wish007/wish007.github.io.git
  branch: master
```



下面是部署的命令

``` bash
hexo clean  # 先清理缓存,有时会因为缓存无法更新主题,实际是删除 Hexo/public 文件夹
hexo g  # 生成静态文件
hexo d  # 部署到GitHub
hexo g -d  # 也可以合在一起
```

部署到 GitHub 实际上是将 `hexo g` 命令生成的 `Hexo/public` 文件夹复制一份到`Hexo/.deploy_git`，然后将`Hexo/.deploy_git`文件夹同步到 `GitHub用户名.github.io` 这个Repository.



部署到 GitHub 仓库：[GitHub - wish007/wish007.github.io](https://github.com/wish007/wish007.github.io)

Hexo 更多命令：https://hexo.io/zh-cn/docs/commands.html