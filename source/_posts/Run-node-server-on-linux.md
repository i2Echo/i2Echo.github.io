---
title: Linux服务器上配置node+mongodb服务
tags: [mongoDB, linux]
categories: [linux]
date: 2017-11-18 12:22:23
---
## 前言: 

准备把一些小项目放到服务器上测试，由于是node+mongodb的项目，未有过部署经验，现记录配置过程。我的服务器系统是centos6 64位，以下均默认在该环境下操作。
<!--more-->

## 安装node

关于node的安装配置，前面已经写过一篇了[linux下安装nodejs](https://ikuyman.pub/2017/07/21/centos-install-nodejs/)

## 安装mongodb

### 下载软件包

``` bash
curl -O https://fastdl.mongodb.org/linux/mongodb-linux-x86_64-3.4.10.tgz
```

### 解压

``` bash
tar -zxvf mongodb-linux-x86_64-3.4.10.tgz
```

### 放到你要运行的位置

``` bash
mkdir -p mongodb
cp -R -n mongodb-linux-x86_64-3.4.10/ mongodb
```

### 将mongodb二进制路径添加到PATH

``` bash
# 不添加path的话你需要进到mongodb的bin目录才能运行相关命令，不方便
export PATH=<mongodb-install-directory>/bin:$PATH
```

记得这里的\<mongodb-install-directory\>替换成你安装的路径

## 运行mongodb

### 创建db数据存放文件夹

``` bash
# mongodb 服务的数据文件夹默认在/data/db下，如果设在别的文件路径，后面启动时需要带路径参数
mkdir -p /data/db
```

### 启动mongodb服务

``` bash
mongod

# 如果数据存放路径不是默认的需要加路径参数
mongod --dbpath <path to data directory>
```

### 使用mongodb

``` bash
# 进入mongo，需要先启动mongodb服务
mongo

# 显示你使用的数据库
db
# 选择或新建要使用的数据库
use <database>
```

后面就可以进行一些增删改查之类的操作了，具体就不多描述，可以去查看官方文档。

## 为node应用配置nginx服务

### nginx配置
由于我的服务器上已经有别的project在运行，我需要用nginx将node服务代理到80端口，这样只要我配置不同的域名，即可实现不同域名访问同服务器的不同应用。下面是配置文件：
node_project.conf

``` bash
server {
  listen      80;
  server_name youromain;

  location  / {
      proxy_pass http://127.0.0.1:3000; #我的node服务时放在默认的3000端口，你可以改成自己的端口
      proxy_set_header Host $host;
      proxy_set_header X-Real-Ip $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_redirect off;
  }
}
```

可以看到nginx的代理配置很简单但是很强大。

### 重启nginx服务

``` bash
service nginx restart
```

看到输出ok就表示配置启动成功。

## 运行node项目

### 从github拉取项目

我一般项目都放github上，要用的时候直接clone到服务器上去，当然服务器也要先装git

``` bash
# clone项目
git clone your-porject-git-address
```

### 在服务器上运行应用

因为都是做好的项目，直接运行就OK了，当然mongodb与node应用之间的配置别忘了设置好。

``` bash
cd yourprojetname
# 安装依赖
npm install
# 启动
node app
```

## 最后

到这里，你已经可以访问你的node项目了，对了，别忘了将域名解析到对应的服务器。

## 参考文献

* [mongodb官方文档](https://docs.mongodb.com)

