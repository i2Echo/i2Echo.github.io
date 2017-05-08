---
title: 关于配置ssh公钥免密登录VPS过程及遇到的坑
tags: [ssh vps linux]
date: 2017-05-08 00:54:28
categories: [Linux]
---
## 前言
最近入手了一个vps，那ss肯定是要装的，但是仅仅装个ss未免太浪费资源，首先想到的是把博客弄上去，然后就可以申请并配置ssl证书使博客加上绿色的小锁锁，即采用https加密访问。然后在服务器搭建远程git仓库过程中，配置ssh公钥免密登录是却一直出现验证失败，还是需要密码；一顿Google之后找到了原因并最终解决，因此记录下来，方便可能遇到此错误的童鞋。<!--more-->

## 本地操作（win10）
请确保已经安装git，不会的网上资料一大把，安装之后会有一个gitbash（window系统），使用下面命令生成公钥和密钥
``` bash
# 现在本地注册一个用户如果未注册过
git config --global user.email "email@example.com" #这里换成你的邮箱
git config --global user.name "username" #username换成一个你喜欢的用户名最好用英文避免以后可能出现的不必要的麻烦
# 生成公钥和密钥
ssh-keygen -t rsa -C "email@example.com" #这里同上替换
```
然后找到你生成的位置，后面要用到生成的公钥id_rsa.pub，一般会在C:\Users\<用户名>.ssh文件夹下。