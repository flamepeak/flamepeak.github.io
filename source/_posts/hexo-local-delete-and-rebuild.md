---
title: Hexo本地删除试图恢复的尝试
date: 2016-08-28 18:33:51
tags: Hexo
categories: [Web前端]
---
新手，好久没更新文章，某天手贱就把本地删了，发现md文件不见了,赶紧把github上的备份成zip到本地，然后就开始折腾
我这种方法要求你从github把username.github.io打包下载下来，保存到本地
基本流程如下：
1.解压username.github.io.zip
2.进入目录username.github.io
3.右键Git Bash here
4.npm install hexo
5.npm install 遇到error：
　　　npm ERR! package.json ENOENT: no such file or directory, open 'username.github.io\package.json'，然后，不知怎么，就随便从主机里搜索一个C:\Program Files\nodejs\node_modules\npm\node_modules\abbrev\package.json复制过去
6.git push -f origin master

最后没有解决。。。
