---
title: Python:使用PIL库时出现The _imagingft C module is not installed 解决办法
date: 2015-06-07 11:56:52
tags: [python,PIL,Pillow]
categories: Python
---
## Enjoy The Error
### 出现问题 
&emsp;&emsp;偶然在网上看到一个*build a python bot that can play web game*的一个`tutorial`,里面是用的一个PIL(python图像处理包)的库来实现获取屏幕。我就想试试效果，安装了PIL库，写了个小Demo。但是一运行，总出现*The _imagingft C module is not installed*的错误。
<!--more-->
### 找出问题
&emsp;&emsp;一开始，我以为是库的版本问题，就换了个版本，可是依然还是报错；于是就问“度娘”找了半天，说是有的依赖库没有编译，需要先安装`libfreetype6-dev`库，再安装PIL，但是基本说的都是Linux下，win下还得使用VC或wingw什么的,算(bu)了(hui)吧(nong)，遂     Google（上不了的童鞋可以使用它的国内镜像站[Glgoo](http://www.glgoo.com/)）之。

### 寻找解决办法
&emsp;&emsp;一番查看，在[stackoverflow](http://stackoverflow.com/questions/4011705/python-the-imagingft-c-module-is-not-installed) 找到了解决办法，就是直接安装编译好的`Pillow for PIL`库 [直戳这里](http://www.lfd.uci.edu/~gohlke/pythonlibs/),亲测可用。

### 解决问题
&emsp;&emsp;进入*http://www.lfd.uci.edu/~gohlke/pythonlibs/*页面，`Ctrl+F`快速找到PIL库  
![PIL](/images/upload/pillow.png "Pillow")
这里我的电脑是64位的，python是py2.7，所以我下载了Pillow-2.8.2-cp27-none-win_amd64.whl这个包.注意到这里是.whl文件，于是到查看下[pip官方文档](https://pip.pypa.io/en/stable/user_guide.html#installing-from-wheels)
找到`installing-from-wheels`，
To install directly from a wheel archive:
``` bash
pip install SomePackage-1.0-py2.py3-none-any.whl
```
于是打开`cmd`or`powershell`进入下载根目录执行：
``` bash
pip install Pillow-2.8.2-cp27-none-win_amd64.whl
```
显示安装成功即可。再次运行demo就会发现不再报错了
下面看看效果：
``` python
# screenshot.py
# -*- coding:utf-8 -*-

from PIL import ImageGrab
import os
import time

def screenGrab():
    im = ImageGrab.grab()
    im.save(os.getcwd() + '\\full_snap__' + str(int(time.time())) +'.png', 'PNG')

if __name__ == '__main__':
    screenGrab()
```
运行就会在当前目录生成全屏截图了：
![screenshot](/images/upload/full_snap__1433665219.png "screenshot")
哈哈，你的问题解决吗