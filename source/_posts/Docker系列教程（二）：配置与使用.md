---
title: Docker系列教程（二）：配置与使用
top: false
cover: false
toc: true
mathjax: true
date: 2020-07-10 16:39:18
password:
summary:
tags: Docker
categories:
---
### Docker配置与使用
<!--more-->
### 1. 非root用户使用
不添加非root用户使用docker时每次都需要输入sudo命令，比较繁琐。这里将docker作为普通/非root用户运行，我们需要添加一个新的系统用户。我们将添加名为"user_name"的新用户，然后将其添加到"docker"组中

添加"user_name"用户
```shell
useradd -m -s /bin/bash user_name
passwd user_name
usrmod -aG docker user_name
```
现在以"user_name"身份登录并运行docker命令：
```shell
su - user_name
docker run hello-world
```
你会从docker那里得到你好世界

### 2. 安装镜像
将基本映像下载到我们的服务器上，请使用以下命令：
```shell
docker pull docker_name
```

### 3. docker运行镜像
有了镜像后，我们就能够以这个镜像为基础启动并运行一个容器。以上面的 ubuntu:18.04 为例，如果我们打算启动里面的 bash 并且进行交互式操作的话，可以执行下面的命令。
```shell
docker run -it --rm ubuntu:18.04 /bin/bash
```
- -it:这是两个参数，一个是-i:交互式操作，一个是-t终端。我们这里打算进入bash执行一些命令并查看返回结果，因此我们需要交互式终端。
- -rm:这个参数说明容器推出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动docker rm。
- ubuntu:18.04:这里指用ubuntu:18.04镜像为基础来启动容器
- bash:放在镜像名后的是命令，我们希望有个交互式的shell，因此用的是bash

### 4. 文件拷贝
将本机文件拷贝到docker镜像中：
```shell
docker cp /opt/test/file.txt docker_id:/opt/testnew/
```