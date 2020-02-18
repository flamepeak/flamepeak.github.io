---
title: Linux 用户权限 相关
date: 2018-08-09 16:53:46
tags: [Linux]
categories: [Linux]
---

### Linux下is not in the sudoers file解决方法
在某个用户（xxx）下使用sudo的时候，提示以下错误：xxx is not in the sudoers file. This incident will be reported。

究其原因是用户没有加入到sudo的配置文件里。

解决方法如下：

1、切换到root用户，运行`visudo`命令  
2、找到`root ALL=(ALL) ALL`，在下面添加一行`xxx ALL=(ALL) ALL` 其中xxx是要加入的用户名称