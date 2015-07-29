---
layout: post
author: Makefile君
title: python学习笔记
category: 挖坑
tags: python
date: 2015-07-29 14:50:09
keywords:
description:
---

# python-learning

##python的__file__

python中的`__file__`,可以用来输出当前的文件名.但是它仅仅是将执行时传递给python解释器的文件路径输出.
也就是说,如果你执行的是`python test.py`,那么得到的结果就是`test.py`, 而如果你执行的是`python test/test.py`,
那么得到的结果就是`test/test.py`. 很多时候,我们想要的是一个绝对路径, 这就需要使用os包中的方法来实现.

注意: 如果是在一个shell中, `__file__`是未定义的.

##python的绝对路径
pytho中可以通过如下的方式来获取指定文件的绝对路径以及指定文件所在文件夹的绝对路径

获取绝对路径

```python
os.path.abspath(__file__)
```

获取所在文件夹的绝对路径

```python
os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
```
