---
title: Linux 温习(四)：Linux计划任务crontab
date: 2016-09-04 16:50:29
tags: Linux
categories: [Linux]
---

通常，crontab 储存的指令被守护进程激活，crond 为其守护进程，crond 常常在后台运行，每一分钟会检查一次是否有预定的作业需要执行。

crontab命令参数：
`crontab [-u user] [-i] { -e| -l| -r}`
> -e 编辑
> -l 显示
> -r 删除
> -i 删除前确认

![crontab_usage](/sourcepictures/20160904/crontab_usage.png)



`crontab -e` 是针对使用者的 cron 來设计的，也就是每个用户在添加任务，就会在 /var/spool/cron/crontabs 中添加一个该用户自己的任务文档，这样可以做到隔离，独立，不会混乱。
如果是系统的例行性任务，只要编辑 `/etc/crontab` 就可以啦
