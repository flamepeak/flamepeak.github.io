---
title: Fiddler 中文乱码解析解决方案
date: 2017-08-11 17:38:48
tags: [Fiddler]
categories: [Tools]
---

在使用Fiddler过程中，遇到中文字符有时会无法解析，显示乱码，左侧列表中的链接有中文也是乱码，在Post参数里也是如此，解决方法如下：

解决方法：  
1.win+R  
2.打开注册表编辑器：输入regedit +回车+是  
3.HKEY_CURRENT_USER\Software\Microsoft\Fiddler2  
4.右键新建，选字符串值，加上HeaderEncoding，然后值输入 GBK 

如果设置为GBK依然不行，请尝试使用UTF-8

