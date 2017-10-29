---
title: webstorm es6 环境配置
date: 2016-09-30 11:33:09
tags: [webstorm, es6, 环境配置]
categories: Tools
---
工欲善其事必先利其器，当然代码编写环境也不例外；转战webstorm一段时间后，用的很顺手；以前用sublim text只能到chrome的控制台调试，也没有代码索引功能（或者你要去装很多插件，但是我发现插件多了每次打开都很慢，一些hint或者lint之类的运行起来编辑器界面卡卡的，反倒不如webstorm这种，虽然启动软件有点慢），所以还是觉得webstorm用起来不错就开始转投webstorm了；当然凡事各有利弊，看个人喜好吧。<!--more-->不过webstorm对于我开发而言，效率得到提高，而且用起来感觉很方便(错误提示，自动补全，注释等等)，这便是我选择的理由了。最近开始看es6比较多，想来webstorm是可以设置es6语法支持的，恩，不会报错了，可是一运行会发现立马报错，看来这是指支持语法规则，而缺少es6的编译环境，这样webstorm的优势完全体现不出来了。想起了万能的互联网，于是搜索到了不少结果，看来也是很多网友遇到这个问题了，幸亏有前辈的肩膀可以站。但是貌似有点高兴太早了，可能电脑各种环境的不一致因素，并未一次成功，踩了几个坑之后，最后还是解决了。

## 语法支持设置
我用的webstorm11(其他版本不保证) 系统win10
 > settings -> Languages & Frameworks -> JavaScript

![配置](/images/upload/setup-es6-1.png)
配置好了之后
![测试](/images/upload/setup-es6-2.png)
好了这样就支持es6语法了，但是你会发现并不能进行debug，编译，运行，很郁闷。这时候就要用到es6的编译器babel了

## babel配置
[Babel](https://babeljs.io/)是一个广泛使用的ES6转码器，可以将ES6代码转为ES5代码，从而在浏览器或其他环境执行。这样你就可以用ES6的方式编写程序，又不用担心现有环境是否支持。
**安装babel**
```
npm install babel-cli
```

**添加babel-preset-es2015模块**
```
npm install babel-preset-es2015
```
项目下必须添加`babel-preset-es2015`模块，不然会报错，然后再添加一个`.babelrc`的配置文件并加上 {"presets":["es2015"]}，这句表示启动时预设es2015转码

## 设置file watcher
如果你希望每次改动源文件时进行自动转码，就需要设置file watcher了
 > settings -> tools -> file watchers

然后点击加号添加
![配置file watcher](/images/upload/setup-es6-3.png)
配置好了之后你就会发现会随时编译成ES5的文件以及sourceMap文件
![转码成ES5](/images/upload/setup-es6-4.png)
这样就大功告成，可以再webstorm下愉快的使用ES6了
