---
title: Linux 技巧：让进程在后台可靠运行的几种方法
date: 2017-08-24 14:50:24
tags:
categories:
---

参考链接： [Linux 技巧：让进程在后台可靠运行的几种方法](https://www.ibm.com/developerworks/cn/linux/l-cn-nohup/index.html)

这里列出screen的几个常用命令：

用`screen -dmS session_name`来建立一个处于断开模式下的会话（并指定其会话名）。  
用`screen -list` 来列出所有会话。  
用`screen -r session_name`来重新连接指定会话。  
用快捷键`CTRL-a d` 来暂时断开当前会话。  


