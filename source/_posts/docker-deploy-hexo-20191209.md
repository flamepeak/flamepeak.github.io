---
title: docker deploy hexo 
date: 2019-12-09 11:07:13
tags: [Hexo, Docker]
categories: [Web前端]
---



## install docker

http://flamepeak.com/2019/11/22/Docker-install-on-kali-20191122/





## install git 、nodejs

```bash
docker pull ubuntu
docker run -itd --name hexo_byMe ubuntu /bin/bash
docker exec -it hexo_byMe /bin/bash
```

```bash
apt-get udpate
# 安装git
apt-get install git-core

#安装nodejs
#https://github.com/nodesource/distributions
# Using Ubuntu
curl -sL https://deb.nodesource.com/setup_12.x | bash -
sudo apt-get install -y nodejs

#检查安装是否成功
node -v
npm
```



## 安装Hexo

```bash
npm install -g hexo-cli
npm install hexo --save
npm install hexo-deployer-git --save
npm install hexo-renderer-pandoc --save
npm install hexo-renderer-marked --save

#如有必要安装pandoc
curl -O -sL https://github.com/jgm/pandoc/releases/download/2.8.1/pandoc-2.8.1-1-amd64.deb 
dpkg -i pandoc-2.8.1-1-amd64.deb
#如果上面命令不行，可以尝试
curl -sL -O http://th.archive.ubuntu.com/ubuntu/pool/universe/p/pandoc/pandoc_1.19.2.4~dfsg-1build4_amd64.deb
dpkg -i pandoc_1.19.2.4~dfsg-1build4_amd64.deb
apt --fix-broken install
npm install hexo-renderer-pandoc --save
```



## 测试Hexo

```bash
hexo g 
hexo s
```

如果在Windows系统，无法访问`localhost:4000`，请参考 []()



## 备份Images

我们可以把container生成images来保存

```bash
docker commit -m="has update" -a="author" e218edb10161 blog_ver2

各个参数说明：
-m: 提交的描述信息
-a: 指定镜像作者
e218edb10161：容器 ID
blog_ver2: 指定要创建的目标镜像名
```







## 一些使用技巧

本地复制文件，可以通过这个命令来实现`docker cp`



### 如何在容器与本地之间 copy 文件？

从容器到本地：

```
docker cp <containerId>:/file/path/within/container /host/path/target
```

从本地到容器：

```
docker cp filename <containerId>:/file/path/within/container
```

以上的操作如果是文件夹，就拷贝的是整个文件夹内容。



