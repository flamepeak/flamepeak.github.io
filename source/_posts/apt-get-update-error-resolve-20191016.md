---
title: kali apt-get update error resolve 20191016
date: 2019-10-16 16:21:49
tags: [Linux, Kali]
categories: [Linux]
---

在更新kali系统时出现如下错误：

```
root@Kali:/usr/xiaocan# apt-get update && apt-get upgrade && apt-get dist-upgrade
```

```
N: Updating from such a repository can't be done securely, and is therefore disabled by default.
N: See apt-secure(8) manpage for repository creation and user configuration details.
```

错误原因是，证书太旧了，需要升级

```
wget -q -O - https://archive.kali.org/archive-key.asc | apt-key add
```

下面是一些国内源：

修改`/etc/apt/sources.list`可以添加

```
#中科大
deb http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
deb-src http://mirrors.ustc.edu.cn/kali kali-rolling main non-free contrib
 
#阿里云
#deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
#deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
 
#清华大学
#deb http://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free
#deb-src https://mirrors.tuna.tsinghua.edu.cn/kali kali-rolling main contrib non-free
 
#浙大
#deb http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free
#deb-src http://mirrors.zju.edu.cn/kali kali-rolling main contrib non-free
```

