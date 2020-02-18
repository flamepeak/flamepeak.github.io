---
title: Wmic collections 20180206
date: 2018-02-06 08:51:33
tags: [Network Security, wmic]
categories: [Network Security]
---

### 获得当前系统已安装的程序列表

[获得当前系统已安装的程序列表](http://www.4hou.com/technology/10206.html)

命令如下：
```bash
wmic /NAMESPACE:"\\root\CIMV2" PATH Win32_Product
```

过滤：  
```bash
wmic /NAMESPACE:"\\root\CIMV2" PATH Win32_Product get name /FORMAT:table
```


### 获取进程参数
在windows下查看某个运行程序（或进程）的命令行参数

```bash
wmic process get caption,commandline /value >> tmp.txt
```

![wmic_temp.PNG])(/sourcepictures/2018/08/15/wmic_temp.PNG)


如果想查询某一个进程的命令行参数，使用下列方式：

```bash
wmic process where caption=”svchost.exe” get caption,commandline /value
```

这样就可以得到进程的可执行文件位置等信息。
