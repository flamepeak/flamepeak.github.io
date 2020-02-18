---
title: Linux apt-get public key is not available 错误解决
date: 2018-02-08 14:53:43
tags: [Linux]
categories: [Linux]
---

`apt-get update`时遇到问题，错误

```
W: GPG error: http://old-releases.ubuntu.com/ubuntu raring Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 40976EAF437D05B5 NO_PUBKEY 3B4FE6ACC0B21F32
E: The repository 'http://old-releases.ubuntu.com/ubuntu raring Release' is not signed.
```

网上找到了答案：

```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 40976EAF437D05B5
```
把对应的key替换执行即可


***

一些源的地址：
修改`/etc/apt/sources.list`即可：

```
deb http://old-releases.ubuntu.com/ubuntu/ raring main universe restricted multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring main universe restricted multiverse

deb http://old-releases.ubuntu.com/ubuntu/ raring-security main universe restricted multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring-security main universe restricted multiverse

deb http://old-releases.ubuntu.com/ubuntu/ raring-updates main universe restricted multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring-updates main universe restricted multiverse

deb http://old-releases.ubuntu.com/ubuntu/ raring-backports main restricted universe multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring-backports main restricted universe multiverse

deb http://old-releases.ubuntu.com/ubuntu/ raring-proposed main restricted universe multiverse
deb-src http://old-releases.ubuntu.com/ubuntu/ raring-proposed main restricted universe multiverse
```
