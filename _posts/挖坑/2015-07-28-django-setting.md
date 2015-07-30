---
layout: post
author: Makefile君
title: django 学习笔记 -- setting文件
category: 挖坑
tags: python django
date: 2015-07-28 17:08:31
keywords:
description:
---

#未完成-待施工

[官方文档看这里](https://docs.djangoproject.com/en/1.8/ref/settings/)

INSTALLED_APP中的label,结尾不能重名.也就是说,'django.contrib.auth'和'myproject.auth'是不能同时被引入的.

使用`django-admin startproject xxx`生成的setting文件中的INSTALLED_APPS如下,

```python
INSTALLED_APPS = (
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
)
```

这里面要说的是django的admin site由'django.contrib.admin'提供,而它依赖于'django.contrib.admin','django.contrib.auth','django.contrib.contenttypes','django.contrib.sessions','django.contrib.messages'这四个app.
