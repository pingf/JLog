---
layout: post
author: Makefile君
title: python change pypi source
category: 挖坑
tags: python pypi
date: 2015-08-02 23:58:14
keywords:
description:
---

# python change pypi source

#pip install 源的修改

国内主要的源就是豆瓣的了(其它好像木有了吧)
`http://pypi.douban.com/simple/`

装包的时候可以使用这样的方式,

```python
easy_install -i http://pypi.douban.com/simple/ gevent
pip install -i http://pypi.douban.com/simple/ gevent
```

或者修改~/.pip/pip.conf成这样设置
```python
[global]
index-url = http://pypi.douban.com/simple
```

但是这样做的话,某些使用python setup.py install安装的依赖依旧不会生效,
需要修改`~/.pydistutils.cfg`为

```python
[easy_install]
index_url = http://pypi.douban.com/simple
```
这是因为distutil中的使用这个文件来配置源,

```python
...
# What to call the per-user config file
  if os.name == 'posix':
      user_filename = ".pydistutils.cfg"
  else:
      user_filename = "pydistutils.cfg"
      ...
```
