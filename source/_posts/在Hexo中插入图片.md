---
title: 在Hexo中插入图片
date: 2016-09-05 00:55:19
categories: Hexo
tags:
- Hexo
---

# 最简便的方法

如果你的Hexo项目中只有少量图片，那最简单的方法就是将图片放在`source/images` 文件夹中；然后在文章中通过类似于 `![](/images/image.jpg)` 的方法访问它们；但是当文章多了以后，这种方式显然不便于管理文章中图片。


<!--more-->


# 更好的方法

Hexo 提供了更组织化的方式来管理资源，可以通过修改`_config.yml`配置文件中的`post_asset_folder`选项为`ture`来打开

```bash
post_asset_folder: true
```

当资源文件管理功能打开后，Hexo将会在你每一次通过 `hexo new <title> `命令创建新文章时自动创建一个文件夹。这个资源文件夹将会有与这个 Markdown 文件一样的名字。将所有与你的文章有关的资源放在这个关联文件夹中之后，你可以通过相对路径来引用它们，这样你就得到了一个更简单而且方便得多的管理方式。

虽然你仍然可以使用常规的 Markdown 语法访问资源，但在 Hexo 中更推荐以下方式引用（图片将会同时出现在文章、主页以及归档页中）

```bash
{% asset_img example.jpg This is an example image %}
```

例如：

{% asset_img mountain.jpg This is an example image %}