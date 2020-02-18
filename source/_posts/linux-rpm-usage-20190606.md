---
title: linux rpm 命令使用
date: 2019-06-06 09:27:39
tags: [Linux]
categories: [Linux]
---

今天看到一个视频，视频在centos中使用yum安装了nginx，然后使用`rpm -ql nginx`查看已经安装的文件（包含路径）。

原来，rpm还可以这样用。

参考：
[Linux rpm 使用详解](https://www.cnblogs.com/xiaochaohuashengmi/archive/2011/10/08/2203153.html)

常用参数：
```
-i, --install                     install package(s)
-v, --verbose                     provide more detailed output
-h, --hash                        print hash marks as package installs (good with -v)
-e, --erase                       erase (uninstall) package
-U, --upgrade=<packagefile>+      upgrade package(s)
－-replacepkge                    无论软件包是否已被安装，都强行安装软件包
--test                            安装测试，并不实际安装
--nodeps                          忽略软件包的依赖关系强行安装
--force                           忽略软件包及文件的冲突

Query options (with -q or --query):
-a, --all                         query/verify all packages
-p, --package                     query/verify a package file
-l, --list                        list files in package
-d, --docfiles                    list all documentation files
-f, --file                        query/verify package(s) owning file
```

常用命令组合：
```
－ivh：安装显示安装进度--install--verbose--hash
－Uvh：升级软件包--Update；
－qpl：列出RPM软件包内的文件信息[Query Package list]；
-ql: 列出已经安装的文件和路径
－qpi：列出RPM软件包的描述信息[Query Package install package(s)]；
－qf：查找指定文件属于哪个RPM软件包[Query File]；
－Va：校验所有的RPM软件包，查找丢失的文件[View Lost]；
－e：删除包
```
===

查询功能：

1、对系统中已安装软件的查询  
1）查询系统已安装的软件；  
语法：rpm -q 软件名  

2）查询一个已经安装的文件属于哪个软件包；  
语法 rpm -qf 文件名  
注：文件名所在的绝对路径要指出  

3）查询已安装软件包都安装到何处；  
语法：rpm -ql 软件名  
或 rpm rpmquery -ql 软件名  

4）查询一个已安装软件包的信息  
语法格式： rpm -qi 软件名

5）查看一下已安装软件的配置文件；  
语法格式：rpm -qc 软件名  

6）查看一个已经安装软件的文档安装位置  
语法格式： rpm -qd 软件名

7）查看一下已安装软件所依赖的软件包及文件  
语法格式： rpm -qR 软件名

---

2、对于未安装的软件包的查看  
查看的前提是您有一个.rpm 的文件，也就是说对既有软件file.rpm的查看等；

1）查看一个软件包的用途、版本等信息
语法： rpm -qpi file.rpm

2）查看一件软件包所包含的文件
语法： rpm -qpl file.rpm

3）查看软件包的文档所在的位置
语法： rpm -qpd file.rpm

4）查看一个软件包的依赖关系
语法： rpm -qpR file.rpm

5）查看一个软件包的配置文件
语法： rpm -qpc file.rpm

