---
title: Python 2 No module named MySQLdb 解决
date: 2018-01-25 10:03:16
tags: [Python, Mysql]
categories: [Python]
---


在使用python写入MySQL数据库是，如果未安装相应模块`import MySQLdb`会报错

下面说一下我的解决办法：

首先，stackoverflow给出了很多解决办法:

[No module named MySQLdb](https://stackoverflow.com/questions/454854/no-module-named-mysqldb)

[Install mysql-python (Windows)](https://stackoverflow.com/questions/21440230/install-mysql-python-windows)

在windows系统上首先应该安装VCForPython27.msi， [下载地址](http://aka.ms/vcpython27)

然后，运行`pip install mysqlclient`；如果报错的话请运行`pip install mysqlclient==1.3.4`，安装低版本的mysqlclient

如果还没有解决你的问题，那请参考stackoverflow上面的解决方案
