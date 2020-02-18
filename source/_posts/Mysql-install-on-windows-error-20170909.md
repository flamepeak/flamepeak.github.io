---
title: Mysql 安装失败 Visual Studio 2013 Redistributable
date: 2017-09-09 09:44:51
tags: [Mysql]
categories: [Web后端]
---

参考文章：[ 装了Visual Studio 2013 Redistributable. MySQL安装失败](http://blog.csdn.net/xx_star1204/article/details/76762175)

在安装Mysql Server过程中，遇到这个奇葩问题，一直显示失败，要求安装Visual Studio 2013 Redistributale.

![mysql errror](/sourcepictures/2017/09/09/mysql_error.png)

然后我从Microsoft网站上下载了2013版本的vcredist_x64.exe，依然显示错误，也是无语了。

以下为引用：

> 问题解决的很迷。。。我在mysql官网上下的installer版老是这问题，然后换了个百度下载的，居然就没事了！！！！

> 旧版本installer百度云链接: https://pan.baidu.com/s/1jIl6Uma 密码: tg6q

有网友提供了可以匹配新版mysql的vs2013 ：

https://support.microsoft.com/en-us/help/3179560/update-for-visual-c-2013-and-visual-c-redistributable-package

致谢！

另外，安装成功之后看了一下 Visual C++ 2013 Redistributable 的版本为 12.0.40660.0