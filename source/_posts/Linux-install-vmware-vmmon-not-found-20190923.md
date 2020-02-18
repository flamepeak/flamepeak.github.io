---
title: Linux install vmware vmmon not found 20190923
date: 2019-09-23 11:44:47
tags: [Linux]
categories: [Linux]
---


在Linux的一个版本中安装Vmware，出现如下错误"Cannot open /dev/vmmon: No such file or directory"，网上有各种解决办法，都不行。

以下这个答案可能比较靠谱[Here](https://askubuntu.com/questions/1096052/vmware-15-error-on-ubuntu-18-4-could-not-open-dev-vmmon-no-such-file-or-dire)，但是因为太繁琐，没有尝试


根本原因是，由于BIOS开启了安全启动，底层的模块没有签名，导致vmware无法开启。

比较暴力，可靠的办法就是，在BIOS关闭EFI 安全启动

大概路径：F12进入BIOS，Security选项卡，找到Security Boot之类的选项，然后关闭即可。
