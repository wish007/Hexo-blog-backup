---
title: 搭建Django博客（5）Markdown语法显示文章
date: 2016-09-05 21:30:40
categories: Django
tags:
- Django
---

安装 Markdown 库

```powershell
$ pip install markdown2
```


<!--more-->


在 blogapp 下建立新文件夹`templatetags`，然后定义自己的 template filter，在`templatetags`中新建`_init.py`, 让文件夹可以被看做一个包，在文件夹中新建`custom_markdown.py`文件并编辑

```python
import markdown2
from django import template
from django.template.defaultfilters import stringfilter
from django.utils.encoding import force_text
from django.utils.safestring import mark_safe

register = template.Library()

@register.filter(is_safe=True)
@stringfilter
def custom_markdown(value):
    return mark_safe(markdown2.markdown(force_text(value), extras=["fenced-code-blocks", "cuddled-lists", "metadata", "tables", "spoiler"]))
```

对模板文件中需要 Markdown 显示的地方增加`custom_markdown`过滤器

```html
{% load custom_markdown %}
.
.
.
{{ post.content|custom_markdown }}
```