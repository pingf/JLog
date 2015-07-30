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

##INSTALLED_APPS

INSTALLED_APPS中的label,结尾不能重名.也就是说,'django.contrib.auth'和'myproject.auth'是不能同时被引入的.

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
(具体可以看django的源码,比如在site.py中引入了auth.views的东西)

##MIDDLEWARE_CLASSES

默认生成的MIDDLEWARE_CLASSES如下,

```python
MIDDLEWARE_CLASSES = (
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
    'django.middleware.security.SecurityMiddleware',
)
```

在看到这里时,我的第一反应是什么是middleware,当然我是知道它的中意的(中间件).
但了解之后知道它在这里就是类似拦截器的功能,有点像spring框架里的interceptor.

假设我们定义了一个middleware,当一个请求到来时,其会先调用middleware的process_request,
然后通过对应的view来处理,最后再调用middleware的process_reponse.

比如我们可以这样定义,然后将其加入到MIDDLEWARE_CLASSES中就行了.

```python
class A(object):
    def process_request(self, request):
        ...
    def process_response(request, response):
        ...
```

另外要注意到是MIDDLEWARE_CLASSES的定义是有先后顺序的.比如再MIDDLEWARE_CLASSES先后定义了两个中间件,
A和B.那么调用顺序是A.process_request->B.process_request->view->B.process_reponse->A.process_response.
有点AOP的感觉了,但是为什么不像flask或bottle那样使用decorator呢?

注:middleware还有很多其它方法,这里不一一罗列了.

嗯,一个app就是一个独立的应用,一个middleware就是一个符合规则的类.一个应用中可以包含一些middleware,也可以不包含.
一个middleware也可以是独立的.


##TEMPLATES

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

默认的TEMPLATES如上, BACKEND用来指定模板引擎.上面用了django自带的引擎.好像django安装时还带了jinja2.
当然你也可以通过对这里的修改将其改成mako之类的模板引擎.
DIR是模板存放的位置,这是一个列表.APP_DIRS则告知是否再app内部查找模板文件.不同的模板有着不同的规则.
OPTIONS则存放了模板引擎相关的具体配置.其中的context_processor是一组在template被渲染时将相关信息添加到context中的一组方法.
比如'django.template.context_processors.static'就是这样定义的.

```python
def static(request):
    return {'STATIC_URL': settings.STATIC_URL}
```
