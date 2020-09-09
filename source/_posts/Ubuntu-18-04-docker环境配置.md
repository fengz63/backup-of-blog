---
title: Docker系列教程（一）：环境配置
date: 2020-06-25 22:25:08
tags: Ubuntu 18.04 Docker
---
## Docker系列教程(一)：环境配置
Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从 Apache2.0 协议开源。Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。
<!--more-->
### 1. 从Docker存储库安装
	Docker目前分为两个版本，docker CE（社区版，免费），docker EE（商业版，收费）
#### 1.1 旧版本卸载
    检查电脑上是否安装有docker，有的话首先卸载旧版的docker，/var/lib/docker/目录下的镜像文件，容器，卷和网络将会被保留，不会被删除。命令如下：
```VB
sudo apt-get remove docker docker-engine docker.io containerd runc
```
### 1.2 安装docker
从docker存储库安装docker-ce之前，首先需要安装一些依赖项
```shell
sudo apt install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
安装完成后，添加docker密钥和docker'nightly'存储库。
```vb
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
echo "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic nightly" > /etc/apt/sources.list.d/docker-nightly.list
```
并更新存储库
```vb
sudo apt update
```
现在docker仓库已经添加到系统中。然后使用apt-get命令去安装它
```vb
sudo apt install docker-ce
```
### 1.3 Docker启动
安装完成后，启动Docker服务并使其每次在系统启动时启动。
```vb
systemctl start docker
systemctl enable docker
```
现在可以检查docker版本
```shell
sudo docker --version
```
{% qnimg docker.png %}