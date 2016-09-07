---
title: 搭建Django博客（4）多说评论框
date: 2016-09-05 21:29:02
categories: Django
tags:
- Django
---

1. 注册[多说](http://duoshuo.com)账号，得到多说`short_name`

2. 在`myblog/templates`文件夹新建`duoshuo.html`并加入代码


<!--more-->


   ```javascript
   <!-- 多说评论框 start -->
   	    <div class="ds-thread" data-thread-key="{{ post.id }}" data-title="{{ post.title }}"></div>
   <!-- 多说评论框 end -->
   <!-- 多说公共JS代码 start  -->
   	<script type="text/javascript">
         <!-- short_name 填自己的多说名字 -->
   	var duoshuoQuery = {short_name:"wish007"};
   	    (function() {
   	        var ds = document.createElement('script');
   	        ds.type = 'text/javascript';ds.async = true;
   	        ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
   	        ds.charset = 'UTF-8';
   	        (document.getElementsByTagName('head')[0]
   	         || document.getElementsByTagName('body')[0]).appendChild(ds);
   	    })();
   	    </script>
   <!-- 多说公共JS代码 end -->
   ```

3. 在需要增加多说评论框的`</div>`标签前面添加以下代码

   ```html
   {% include "duoshuo.html" %}
   ```