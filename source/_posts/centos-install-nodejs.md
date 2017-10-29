---
title: Centos下安装nodejs并升级到最新稳定版
tags: [centos, nodejs, linux]
date: 2017-07-21 22:59:18
categories: [Linux]
---
## 前言：
本文主要记录下在centos6上安装nodejs并实现node版本管理的过程。
<!--more-->
## 一、使用源码安装

### 准备
``` bash
yum -y install gcc make gcc-c++ openssl-devel wget
```

### 下载源码并解压
``` bash
wget http://nodejs.org/dist/v6.4.4/node-v6.4.4.tar.gz
# 此处版本号可以更换你想安装的
tar -zvxf node-v6.4.4.tar.gz
```
### 编译&安装
``` bash
# 注意使用node权限
make && make install
```
### 验证安装成功
``` bash
node -v
# 显示版本号即安装成功
```

## 二、使用yum安装
### 安装nodejs
``` bash
yum install -y nodejs
```
### 查看版本
``` bash
node -v
```
### 安装node版本管理工具n
``` bash
npm install -g n
```
### 升级node为最新稳定版
``` bash
n stable
```
### 查看是否升级成功
``` bash
node -v
# 显示最新版本号v8.2.1
```
这样就OK了，可以愉快的撸node了

## 参考文献
* [Centos 安装 NodeJS](http://www.cnblogs.com/hamy/p/3632574.html)
* [利用n和nvm管理Node的版本](http://weizhifeng.net/node-version-management-via-n-and-nvm.html)
