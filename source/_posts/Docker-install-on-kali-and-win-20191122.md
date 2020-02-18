---
title: Kali Win 安装Docker和Docker-Compose
date: 2019-11-22 09:16:49
tags: [Docker]
categories: [Linux]
---



## On Kali

### Preparation

Add Docker PGP key:  

```bash
curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
```



Configure Docker APT repository (Kali is based on Debian testing, which will be called buster upon release, and Docker now has support for it):

```bash
echo 'deb [arch=amd64] https://download.docker.com/linux/debian buster stable' > /etc/apt/sources.list.d/docker.list
```



```bash
apt-get update
```



### Install Docker

If you had older versions of Docker installed, uninstall them:

```bash
apt-get remove docker docker-engine docker.io
```

Install Docker:

```bash
apt-get install docker-ce
```



#### Test

```bash
docker run hello-world
```

After installation, Docker service will be started, but not enabled (i.e. it will not be started automatically after reboot). To start it:

```bash
systemctl start docker
```

To start Docker automatically upon reboot (do it on your own risk!):

```bash
systemctl enable docker
```



---



### Install Docker-Compose

#### Install

1. Run this command to download the current stable release of Docker Compose: 

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```



2. Apply executable permissions to the binary:

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

 **Note**: If the command `docker-compose` fails after installation, check your path. You can also create a symbolic link to `/usr/bin`or any other directory in your path. 

```bash
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```



#### Test

```bash
$ docker-compose --version
docker-compose version 1.24.1, build 1110ad01
```



## On Win

win7、win8 等需要利用 docker toolbox 来安装，国内可以使用阿里云的镜像来下载，下载地址：http://mirrors.aliyun.com/docker-toolbox/windows/docker-toolbox/

官网下载的desktop安装包，无法直接在win7运行，可以在win10运行

docker toolbox 是一个工具集，它主要包含以下一些内容：

```
Docker CLI 客户端，用来运行docker引擎创建镜像和容器
Docker Machine. 可以让你在windows的命令行中运行docker引擎命令
Docker Compose. 用来运行docker-compose命令
Kitematic. 这是Docker的GUI版本
Docker QuickStart shell. 这是一个已经配置好Docker的命令行环境
Oracle VM Virtualbox. 虚拟机
```

 注意：安装路径千万不要有中文！！！！ 

可以参考：[Windows7下安装Docker、下载镜像和运行OpenTsdb容器]( [https://blog.chemcyber.com/2017/05/18/windows7%E4%B8%8B%E5%AE%89%E8%A3%85docker%E3%80%81%E4%B8%8B%E8%BD%BD%E9%95%9C%E5%83%8F%E5%92%8C%E8%BF%90%E8%A1%8Copentsdb%E5%AE%B9%E5%99%A8/](https://blog.chemcyber.com/2017/05/18/windows7下安装docker、下载镜像和运行opentsdb容器/) )

 通过Git Bash(CMD)查看docker是否安装成功，输入`docker-machine`，出现版本等信息，安装能够运行，还会给出一些选项，包括machine安装路劲等 

查看一些配置：

```bash
docker-machine config
```





