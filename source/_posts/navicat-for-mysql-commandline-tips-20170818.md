---
title: Navicat for mysql 命令行界面使用介绍
date: 2017-08-18 11:05:32
tags: [Tool, Mysql, Navicat]
categories: [Tools]
---

本文主要参考：[使用数据库管理工具navicat----命令行界面](http://blog.csdn.net/hlsdbd1990/article/details/47955751)

命令行界面使用起来有很多优势，在普通界面速度慢，或者实现普通界面难以实现的问题时，命令行界面优势很明显

1. 先打开navicat，建立好连接，我MySQL服务器就是装在自己的机子上的，所以连接localhost；

2. 使用快捷键F6或者在“工具”----“命令列界面”打开命令列界面

3. 注意在使用navicat的命令行界面时，输入的命令末尾必须加上" ; "(英文格式的分号)；

4. 可以输入的命令有：

（1）查看当前的数据库，命令：  `show databases; `

（2）进入到某个数据库，命令：  `use 数据库名;`

（3）进入到某个表中，   命令：  `show 表名;`

![1.png](/sourcepictures/2017/08/18/20150824195856985.png)

（4）查询某个表中的数据--select, 命令：`select....`

（5）建表，命令：`create table table_name`

(6) `truncate table table_name` 清空表table_name,并且id会从1开始


