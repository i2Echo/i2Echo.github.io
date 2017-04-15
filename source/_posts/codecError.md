---
title: 遇到'gbk' codec can't encode character u'\xa0'及类似错误解决办法
date: 2015-06-08 18:51:29
tags: python
categories: Python
---

&emsp;&emsp;一般像这种错误我经常会在用爬虫爬东西的时候遇到，抓过来的数据可能会很乱，当终端输出时就很可能出现`'gbk' codec can't encode character u'\xa0'`及类似的编码错误。因为终端默认输出编码为gbk，而gbk里并没有`\xa0`这个字符，下面总结几种常用的解决办法：
<!--more-->
##利用python的replace方法替换不规则数据
``` python
str = u'\xa0'u'\u4e00'
print str.replace(u'\xa0', '') 
```

##运用`encode`函数
``` python
str = u'\xa0'u'\u4e00'
print str.encode('gbk','ignore')
# 如果你想采用Unicode编码，你可以这样
newStr = str.encode('gbk','ignore').decode('gbk')
```

其实你也可以用万能的正则表达式了，不过暂时感觉杀鸡焉用牛刀了，非要用的话也可以试试，这里就不详细说明了。
