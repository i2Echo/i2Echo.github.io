---
title: 关于配置ssh公钥免密登录VPS过程及遇到的坑
tags: [ssh vps linux]
date: 2017-05-07 00:54:28
categories: [Linux]
---

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

## vps(centos6)
### 安装git
``` bash
yum update && apt-get upgrade -y
yum install git-core
```
### 新增git用户（管理git仓库）
``` bash
adduser git
#修改权限
chmod 740 /etc/sudoers
vi /etc/sudoers
# 找到以下内容
## Allow root to run any commands anywhere
root    ALL=(ALL)     ALL
# 增加一行
git   ALL=(ALL)     ALL

# 退出保存并改回权限
chmod 440 /etc/sudoers
```
### 新建远程仓库，并配置ssh免密登录
su git
cd ~
mkdir .ssh && cd .ssh
touch authorized_keys
vi authorized_keys//在这个文件中粘贴进刚刚申请的key（id_rsa.pub文件中）
cd ~ 
mkdir hexo.git && cd hexo.git
git init --bare # 创建裸仓库

### 远程免密登录
``` bash
# 在gitbash中输入
ssh git@VPS的IP地址
```
如果登录成功表示配置成功,但是我弄了半天还是要求你输入密码，经过一番查找，找到了原因，就是文件权限配置有要求。
根据文档，还需要配置服务器上的文件权限
``` bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys 
```
以上表示只有该文件所有者才能拥有读写或执行的权限
设置完成之后连接成功，不需要密码登陆了
![](\images\upload\ssh-no-password.png)

## 参考
[在VPS上搭建hexo博客，利用git更新](http://tiktoking.github.io/2016/01/26/hexo/)

[Securing OpenSSH](https://wiki.centos.org/HowTos/Network/SecuringSSH)