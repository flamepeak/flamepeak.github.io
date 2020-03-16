---
title: Linux Volatility 安装与使用
date: 2020-03-11 16:08:12
tags: [Linux]
categories: [Linux]
---

[github: volatility](https://github.com/volatilityfoundation/volatility)


### 安装

### Linux

在kali中，安装过程很简单

```bash
apt-get install volatility
```

如果需要可以安装各种依赖，当然可以不安装

```bash
#Distorm3:牛逼的反编译库
pip install distorm3

#Yara:恶意软件分类工具
pip install yara

#PyCrypto:加密工具集
pip install pycrypto

#PIL:图片处理库
pip install pil

#OpenPyxl:读写excel文件
pip install openpyxl

#ujson:JSON解析
pip install ujson
```

### Windows

直接至官网下载exe文件， [Volatility 2.6 (Windows 10 / Server 2016)](https://www.volatilityfoundation.org/releases)



## 用法

```bash
volatility -f mem.raw imageinfo   #获取镜像系统信息

# pslist/pstree/psscan 进程信息
# pslist无法显示隐藏/终止进程。解决这个问题的插件是psscan，这个插件输出的内容会比pslist多得多。
# pstree同样也是扫描进程的，但是是以进程树的形式出现的。

volatility -f mem.raw  --profile=Win7SP1x64 pslist # 列出所有进程
volatility -f mem.raw  --profile=WinSP1x64 cmdscan #查看命令行进程中历史命令
volatility -f mem.raw --profile=WinXPSP3x86 filescan | grep "flag"  #文件搜索，并过滤
volatility -f mem.raw --profile=WinXPSP3x86 dumpfiles -Q 0x0000000001155f90 --dump-dir=./ #导出指定偏移的文件
volatility -f mem.raw  --profile=WinSP1x64 hivelist #列举缓存在内存中的注册表

#查看系统用户名
volatility -f mem.raw --profile=Win7SP1x64 printkey -K "SAM\Domains\Account\Users\Names"
#查看网络连接
volatility -f mem.raw --profile=Win7SP1x64 netscan

```



