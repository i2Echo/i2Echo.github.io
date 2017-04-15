---
title: 使用python tailer库实时读取文件
date: 2015-06-07 00:51:50
tags: [python,tailer]
categories: Python
---

[**Tailer**](https://github.com/six8/pytailer)库是一个第三方的python开源库，它主要提供三个函数：
1. `tail` - 从文件的尾部读取数据	
2. `head` - 从文件的头部读取数据
3. `follow` - 跟踪文件变化并读取增长的行

这里我们就是用到它的`follow`函数来跟踪文件变化。
<!--more-->
## 使用

### 安装
使用`pip`或者`easy_install`工具安装

``` bash
  pip install tailer  # or easy_install tailer 
```

### Demo  

本示例实现了实时读取`msg.txt`文件内容并打印到控制台;
话不多说，直接上代码了.

``` python
# readmsg.py
# -*- coding:utf-8 -*-

import tailer

filename = "msg.txt"
# follow函数跟踪读取文件变化
for line in tailer.follow(open(filename)):
    print line
```
为了更好的查看效果，这里我写了个`gettime.py`文件来不断向`msg.txt`写入当前系统时间；
``` python
# gettime.py
# -*- coding:utf-8 -*-

import time,random

def GetNowTime(): # 获取当前系统时间
    nowtime = time.strftime("%Y-%m-%d %H:%M:%S",time.localtime(time.time()))
    return nowtime

def write2txt(txt): # 文件追加写入
    filename = "msg.txt"
    f = open(filename, 'a')
    try:
        f.write('\n'+txt)
    finally:
        f.close()

def main():
    while True:
        txt = GetNowTime()
        print u'发送系统时间：'+txt
        write2txt(txt)
        time.sleep(random.randint(1,5)) # 随机延时1-5秒

if __name__ == '__main__':
    main()
```

### 运行效果
*Run* `gettime.py`
![GetNowTime](/images/upload/gettime.png "GetNowTime")  
*Run* `readmsg.py`
![ReadMsg](/images/upload/readmsg.png "readmsg")
可以看到控制台实时打印输出了`msg.txt`文件的内容变化。

### 最后
&emsp;&emsp;这个模块对于通过简单的监控文件变化`tailer.follow`来得出某些信息的应用情形是非常轻量级的解决方案，与之对应的是使用专业的队列程序。不过对于简单的应用情形来看(*比如说可以应用直播弹幕助手上*)，这个库就可以满足需求了。