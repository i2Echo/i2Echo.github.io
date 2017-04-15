---
title: 全宋词之东风何处是人间
date: 2015-06-10 03:15:00
tags: [python,scrapy]
categories: Python
---

&emsp;&emsp;前几天在网上看到一篇文章，说的是一位叫`yixuan`的网友统计出了宋词词频，并且依据这些统计结果，网友纷纷在下面作词留言，反正聊得挺欢乐的，详情请戳[这里](http://yixuan.cos.name/cn/2011/03/text-mining-of-song-poems/)。顿时呢我也兴趣来了，也想用python(他是用的R语言)自己来实践一下，顺便做几首词出来装装逼。好的，那我们开始了！
<!--more-->
## 使用爬虫获取全宋词

### 使用工具scrapy
最近刚好学了`scrapy`爬虫框架，正好拿来用用。了解更多关于scrapy的知识，可查看[官方教程](http://doc.scrapy.org/en/latest/intro/tutorial.html)；具体使用就不再详细说明了。

### 分析网页结构
好的，废话就不多说了；先找到提供全宋词的网站，[点击这里](http://www.gushiwen.org/gushi/quansong.aspx)。
然后在全宋词页面定位诗人，审查元素，如下图所示![songci](/images/upload/songci.png "songci")
可以发现，链接都有规律，就可以直接放到scrapy的starURL列表里。下面就直接代码感受一下了。

### 开始编写代码
``` python
# songciSpider.py
# -*- coding:utf-8 -*-
__author__ = 'echolee'

import scrapy
from scrapy.selector import Selector

from scrapy.spider import Spider
from songci.items import *

class newsInfoSpider(Spider):
    name = "songci"
    url_list = []
    allowed_domains = ["gushiwen.org"]

    def start_requests(self):
        url_template = 'http://www.gushiwen.org/wen_%s.aspx'
        for i in xrange(2011, 3363):  # page:2011-3362
            yield scrapy.Request(url_template % i, callback=self.parse)

    def parse(self, response):
        sel = Selector(response)

        all_ptag = sel.xpath("//div[@class='authorShow']/p")
        author = all_ptag.xpath('text()').extract()[0]
        all_pci = all_ptag.xpath('string(.)').extract()[1:]
        all_ci = ' '.join(all_pci).encode('gbk','ignore').decode('gbk')
        url = response.url
        item = SongciItem()
        item['author'] = author
        item['url'] = url
        item['all_ci'] = all_ci

        return item
```
``` python
# pipelines.py
# -*- coding:utf-8 -*-
__author__ = 'echolee'
import json
import codecs

class SongciPipeline(object):
    def __init__(self):
        self.file = codecs.open('songci.json', 'w', encoding='utf-8')
    def process_item(self, item, spider):
        line = json.dumps(dict(item), ensure_ascii=False) + "\n"
        self.file.write(line)
        return item
    def spider_closed(self, spider):
        self.file.close()
```
这里不仅仅提取了诗文，还有作者标题等方便别的用途。

### 运行
打开`cmd` or `powershell`，进入scrapy project目录，执行命令
``` bash
scrapy crawl spiderName # 这里的spiderName是自己定义的
```
然后等待抓取完毕保存为json文件即可。
## 数据预处理
&emsp;&emsp;这一节，我们会说到一些简单的文件操作，包括文件读取与写入；还有中文正则匹配，这个中文正则，在我的前一篇文章里有提到过的，[传送门](/2015/06/08/Re/);当然还涉及到json模块解析json数据；下面可以参考我的代码试试。
### 读取json文件
``` python
# 读取json文件，提取所有词文
def json2str():
    file_path = 'songci.json'
    fo = open(file_path, 'r')
    lines = fo.readlines()
    fo.close()
    allci_line = []
    for line in lines:
        jsonline = line.strip()
        data = json.loads(jsonline)
        ci = data['all_ci']
        # print ci
```
### 词句初步处理
去标点并以`，。`为分割点把词分为一行一句式方便后续处理
``` python
# 因为有的词里面有标题以及括号里的副标题
# 不同的的提取会影响最终结果，所以我这里就三种方式都有(唉，强迫症患者，没办法)
# 你也可以不用理会，用一个就行了
def pick2line(str, flag = 1):
    # flag
    # 0表示全取，1表示只取正词，2表示词+正标题
    if flag == 0:
        pick = re.compile(u"[\u4e00-\u9fa5]+")
    if flag == 1:
        pick = re.compile(u"[\u4e00-\u9fa5]+(?=[\u3002|\uff0c|\u3001]{1})")
    if flag == 2:
        pick = re.compile(u"[\u4e00-\u9fa5]+")

        str = str.replace(u'·','')
        pickbetter = re.compile(u"[\uff08]{1}[\u4e00-\u9fa5]+[\uff09]{1}")
        str = pickbetter.sub('',str)

    line = pick.findall(str)

    return line
```

### 保存为txt文件
``` python
def write2txt(lines):
    if not os.path.exists('songcidata'):
        os.mkdir("songcidata")
    filename = "songcidata/songci_line.txt"
    f = open(filename, 'w')
    try:
        f.writelines(lines.encode('utf8'))
    finally:
        f.close()
```

### 全部的代码
``` python
# -*- coding:utf-8 -*-
__author__ = 'echolee'

import json, re, os, time


def pick2line(str, flag):
    # flag
    # 0表示全取，1表示只取正词，2表示包括正标题
    if flag == 0:
        pick = re.compile(u"[\u4e00-\u9fa5]+")
    if flag == 1:
        pick = re.compile(u"[\u4e00-\u9fa5]+(?=[\u3002|\uff0c|\u3001]{1})")
    if flag == 2:
        pick = re.compile(u"[\u4e00-\u9fa5]+")

        str = str.replace(u'·','')
        pickbetter = re.compile(u"[\uff08]{1}[\u4e00-\u9fa5]+[\uff09]{1}")
        str = pickbetter.sub('',str)

    line = pick.findall(str)

    return line

def json2str(flag):
    file_path = 'songci.json'
    fo = open(file_path, 'r')
    lines = fo.readlines()
    fo.close()
    allci_line = []
    for line in lines:
        jsonline = line.strip()
        data = json.loads(jsonline)
        ci = data['all_ci']
        # print ci
        ci_lines = pick2line(ci, flag)
        for ci_line in ci_lines:
            allci_line.append(ci_line)

    return '\n'.join(allci_line)

def write2txt(lines,name):
    if not os.path.exists('songcidata'):
        os.mkdir("songcidata")
    filename = "songcidata/"+name+".txt"
    f = open(filename, 'w')
    try:
        f.writelines(lines.encode('utf8'))
    finally:
        f.close()

def main():

    namelist = ['songci_haveall','songci_notitle','songci_havebettertitle']
    t0 = time.time()
    for flag in xrange(3):
        txt = json2str(flag)
        # print txt
        write2txt(txt,namelist[flag])

    print '数据处理完成,用时%s s' %( time.time() - t0 )

if __name__ == '__main__':
    main()
```

好的，经过上面的处理我们已经得到以句为分割的宋词行的txt文本了（我这里只取正文的话，总共有277950句）。
## 分词与分析
&emsp;&emsp;终于，我们要开始正题了么，没错，希望你前面的代码都理解好了；其实很简单有没有;这一节你会发现宋词也挺有趣的；会了解到怎么使用sqlite数据库;还有基于统计的简单分词。
### 分词
&emsp;&emsp;中文分词一直以来都是一个比较有挑战性的问题，我只是做个简单的宋词词频统计而已，所以并不想过多的纠结于分词的问题上；因为宋词的句子一般都比较短，就偷懒直接上暴力的穷举法；简单来讲就是对一个句子进行所有可能的划分，再通过词频高低简单判断并丢弃低频词(词频越高越有可能是一个词，反之则丢弃)。
好的，下面就简单的用Python实现一下：
``` python 
# 对每句进行穷举分词
def fenci(line):
    line = line.decode('utf-8').replace(u'\n', '')
    for i in xrange(len(line)):
        for j in xrange(i, len(line)+1):
            key = line[i:j]
            if not key:
                continue
            if key in fenci_dict:
                fenci_dict[key] += 1
            else:
                fenci_dict[key] = 1
            # print '>>>'+key
```
### 使用sqlite数据库
为了后面使用方便查询，这里使用sqlite数据库储存分词结果
``` python 
def sava2db():
    # 使用splite3数据库保存数据方便查询
    conn = sqlite3.connect('songcidata/songci.db')
    cursor = conn.cursor()
    # 创建table如果不存在
    cursor.execute("create table if not exists songci(key text, value inteager)")

    for index, key in enumerate(fenci_dict):
        if fenci_dict[key] < 10:  # 去低频
            continue
        if cursor.execute('select * from songci where key=?', (key,)).fetchall():
            continue
        cursor.execute('insert into songci values(?,?)', (key, fenci_dict[key]))
    conn.commit()
    conn.close()
    print '写入数据库成功...'
```
### fenci.py全部的代码
``` python 
# -*- coding:utf-8 -*-
__author__ = 'echolee'

import sqlite3, time

def fenci(line):
    line = line.decode('utf-8').replace(u'\n', '')
    for i in xrange(len(line)):
        for j in xrange(i, len(line)+1):
            key = line[i:j]
            if not key:
                continue
            if key in fenci_dict:
                fenci_dict[key] += 1
            else:
                fenci_dict[key] = 1
            #print '>>>'+key
def sava2db():
    # 使用splite3数据库保存数据方便查询
    conn = sqlite3.connect('songcidata/songci2.db')
    cursor = conn.cursor()
    # 创建table如果不存在
    cursor.execute("create table if not exists songci(key text, value inteage)")

    for index, key in enumerate(fenci_dict):
        if fenci_dict[key] < 10:  # 去低频，10次以下的词频说明不是词或者意义不大
            continue
        if cursor.execute('select * from songci where key=?', (key,)).fetchall():
            continue
        cursor.execute('insert into songci values(?,?)', (key, fenci_dict[key]))
    conn.commit()
    conn.close()
    print '写入数据库成功...'

def main():
    filename = 'songcidata/songci_line.txt'
    fo = open(filename, 'r')
    lines = fo.readlines()
    fo.close()
    global fenci_dict # 使用全局变量储存结果
    fenci_dict = {}
    for line in lines:
        fenci(line)
    # total = len(fenci_dict)
    # 排序输出前十
    # print '总分词结果为',total,'条\n前十条结果为：'
    # sorted_fenci = sorted(fenci_dict.iteritems(), key = lambda d: d[1], reverse = True)
    # for i in xrange(10):
        # print sorted_fenci[i][0],':', sorted_fenci[i][1]
    sava2db()

if __name__ == '__main__':
    t0 = time.time()
    main()
    print '数据处理完成,用时%.2f s' %( time.time() - t0 )
```
### 输出排序结果
话也就不多说了，直接进入最激动人心的时刻吧，等一下，还是先看看代码了：
``` python 
# -*- coding:utf-8 -*-
__author__ = 'echolee'

import sqlite3

def getDoubleSort():
    conn = sqlite3.connect('songcidata/songci.db')
    cursor = conn.cursor()
    # 查询所有两个字组合的词并排序
    sql = 'select * from songci where length(key)=2 order by value desc'
    result = cursor.execute(sql).fetchall()
    conn.close()

    rank = '000000' # 这里纯属强迫症晚期，只是为了输出时对齐好看
    rank = rank[:len(str(len(result)))]
    # 词频从高到低排序输出
    print ' Rank ',' Word ',' Freq'
    for i in xrange(len(result)):
        print rank[len(str(i+1)):]+str(i+1),'-',result[i][0],':', result[i][1]

if __name__ == '__main__':
    getDoubleSort()
```
好的，该来的总是要来的，OK，我们运行看看结果：
![rank](/images/upload/rank2.png "rank")
看到这个排名，是不是感到一股亲切感，没错；前三`东风`、`何处`、`人间`，这就是全宋词所隐藏的文字密码，哈哈，全宋词总结起来就是一句话：`东风何处是人间`（哈哈，装个逼，文科生们请手下留情）。

再来看看三个字的（查询字长改为3即可）：
![rank](/images/upload/rank3.png "rank")
哈哈，3字词频最高的居然是`倚阑干`，为何这些诗人都爱`倚阑干`啊，正如马亲王说的：“千古忧愁，不过五个字：独自莫凭栏。”
正好在知乎看到过这个问题-[古代诗词大家为何都爱倚栏杆（阑干）?](http://www.zhihu.com/question/23858540),下面有的回答挺好的。

对了，有了词频表，也可以自己作作词玩玩了，比如说，数字随机生成词，那就写个简单demo试试：
``` python 
# -*- coding:utf-8 -*-
__author__ = 'echolee'

import sqlite3

def num2songci(num):
    search = []
    for x in xrange(0, len(num), 2):
        search.append(int(num[x:x+2]))

    conn = sqlite3.connect('songcidata/songci.db')
    cursor = conn.cursor()
    for key in search:
        # 每次以第(key-1)条记录为基准取一条记录，
        sql = 'select * from songci where length(key)=2 order by value desc limit 1 offset ' + str(key-1)
        print cursor.execute(sql).fetchall()[0][0],
    conn.close()

```
比如，把我生日19931211输入，就是“`梅花千里，行人一笑`”（当然这里的词序是我自己组合的），还真有点词的味道哦；
再来个根号二小数点后几位(`4142135623730950488016887242097`)
再稍微润色一下，即可得如下：

    如梦令·根号二

    平生凄凉（感叹凄凉的一生）
    回首何事（还能回想什么事呢）
    芳草海棠（不就是那些值得回忆的红颜知己）
    相思杨柳（还有那些甚是思念的友人）
    西湖无情（想起当年西湖断桥的白素贞和许仙，为什么那么无情呢）
    如今往事（现在想起以前的种种往事）
    一声凄凉（只能再叹一声凄凉）
    相思西风（还有那思念仍飘荡在这寒冷的西风中）

还挺有趣的吧,那你也可以试一试的；其实的话这里做的比较简陋，比如平仄押韵什么的都没有，如果你有兴趣的话，还可以做得更好，and 欢迎在下面留言！
