---
layout: post
author: Makefile君
title: matias tactile pro键盘ubuntu下改键大法
category: 挖坑
tags: ubuntu tactile xmodmap
date: 2015-07-23 17:08:31
keywords:
description:
---

# matias tactile pro键盘ubuntu下改键大法

用惯了mac之后再换回ubuntu, 多少又有些不习惯了.
记得当时初用mac时也是这个感觉,但是现在已经适应mac了,以及那坑跌的nature scroll !!!

所以现在要保留那些坑跌得习惯,加之又买了tactile for mac的大白键盘.

所以首先要解决两个问题,一个是键盘control和cmd/windows键得互换,另一个是nature scroll的支持.

好在linux下面有着xmodmap这样得神器, 轻松搞定不是问题!

把下面这些写入一个文件,然后用`xmodmap 文件名`,即可完成修改

```bash
clear control
clear mod4

keycode 133 = Control_L
keycode 134 = Control_R
keycode 37 = Super_L
keycode 105 = Menu

add control = Control_L
add control = Control_R
add mod4 = Super_L
add mod4 = Menu

pointer = 1 2 3 5 4 6 7 8 9 10 11 12
```

注: 上面的keycode可以通过xev来获得, 而add后面得modifier我还没整明白从哪里整的

tactile键位互换后,下排依次是

```bash
cmd/window,  alt_l, ctrl_l, space, ctrl_r, alt_r, context_menu
```

最后一个是最让我激动得,因为是久违了的context_menu键!!!(这或mac下面是木有的撒!)

tactile pro大白其它的功能键对ubuntu支持的很好,完全无需任何的修改.

最后pointer那行,是对鼠标滚轮进行修改的, 触摸板得nature scroll则通过ubuntu自带得设置就能完成了.

另外, mac下面需要sizeup这样工具才能完成的窗体操作,ubuntu的unity自带了.而iterm这样的工具, 装个terminator就可以了(但是这货坑爹的不支持查找高亮)

其它的要改的东西并不多,大JB的IDE的快捷键要改一些,还是比较费事儿的.

不过换成ubuntu最大的好处是,玩docker更加畅快了!
