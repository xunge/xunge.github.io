---
title: Django报错 __init__() missing 1 required positional argument 'on_delete' 
tags:
  - Django
categories: 经验值
updated: '2017-12-23 09:01:42'
date: 2017-12-23 09:01:42
---

Django 在更新到 2.0 后 报错如下

```
TypeError: __init__() missing 1 required positional argument: 'on_delete'
```

<!--more-->




是因为在 Django 2.0 后，models.ForeignKey() 函数 和 models.OneToOneField() 中的 on_delete 参数不再默认为 CASCADE ，而是必须参数

官方文档：[https://docs.djangoproject.com/en/1.11/ref/models/fields/#django.db.models.ForeignKey.on_delete](https://docs.djangoproject.com/en/1.11/ref/models/fields/#django.db.models.ForeignKey.on_delete)


修改方法如下


修改之前

```py
class BlogArticles(models.Model):
    title = models.CharField(max_length=300)
    author = models.ForeignKey(
        User,
        related_name="blog_posts",
    )
```

修改之后

```py
class BlogArticles(models.Model):
    title = models.CharField(max_length=300)
    author = models.ForeignKey(
        User,
        related_name="blog_posts",
        on_delete=models.CASCADE,
    )
```

