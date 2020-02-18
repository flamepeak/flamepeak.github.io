---
title: openSUSE 使用问题与解决技巧
date: 2016-12-16 15:33:20
tags: [openSUSE, Linux]
categories: [Linux]
---

### 截图、截屏工具
在openSUSE 42.1版本中的截图工具为ksnapshot，但是在openSUSE 42.2中截图工具更改为spectacle。
`ksnapshot < 16.08.2 is obsoleted by (installed) spectacle-16.08.2-1.1.x86_64`

### 安装使用SublimeText 3
SublimeText提供了deb安装包和bz2的压缩包，下面介绍压缩包的使用：
首先解压缩，
```
tar -xvf sublime_text_3_build_3126_x64.tar.bz2
```
然后，将解压后的文件放到合适的位置。
```
mv sublime_text_3  /opt
```
最后，我们使用命令行创建一个*symbolic link*：
```
ln -s /opt/sublime_text_3/sublime_text /usr/bin/sublime
```
这样，我们就可以在命令行中直接输入**sublime**即可使用。
如果需要加入Unity luncher,那就:
```
cp /opt/sublime_text_3/sublime_text.desktop  /usr/share/applications
```

### SublimeText 
Markdown highlight : Monokai Extended