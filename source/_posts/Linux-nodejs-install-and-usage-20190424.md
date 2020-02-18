---
title: Linux nodejs install and usage
date: 2019-04-24 09:11:07
tags:
categories: [Linux]
---

因为要在Linux下安装nodejs指定版本，遇到一些问题，下面说一下解决办法：

1. apt-get安装
apt-get install nodejs

这种方法可以安装，但是安装的最近稳定版本，

apt-get install nodejs=8.16.0

提示源找不到。

2. 本地安装

[参考：在Linux系统安装Nodejs 最简单步骤](https://www.cnblogs.com/liuqi/p/6483317.html)

但是上面这篇文章，不知道在哪一个Linux版本下安装的，在我的kali（Debian）中建立软链接有问题，在此修改

官网下载指定版本，然后运行下面命令
```
tar zxvf node-v8.16.0-linux-x64.tar.gz
mv node-v8.16.0-linux-x64 nodejs
mv nodejs /usr
ln -s /usr/nodejs/bin/npm /usr/bin
ln -s /usr/nodejs/bin/node /usr/bin
```
可以使用node -v 和npm-v 进行检查，是否显示版本