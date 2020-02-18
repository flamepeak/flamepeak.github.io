---
title: Linux Check kernel version and distribution 20180809
date: 2018-08-09 10:16:20
tags: [Linux]
categories: [Linux]
---

### Linux 
Open a terminal program (get to a command prompt) and type `uname -a`. This will give you your kernel version, but might not mention the distribution your running. To find out what distribution of linux your running (Ex. Ubuntu) try `lsb_release -a` or `cat /etc/*release` or `cat /etc/issue*` or `cat /proc/version`.

### FreeBSD/NetBSD/OpenBSD/DragonFlyBSD
Open a terminal program (get to a command prompt) and type `uname -a`. This will tell you the version (release) and type of BSD your running.

### Solaris (SunOS)
Open a terminal program (get to a command prompt) and type `uname -a` or for lots of info (on newer Solaris machines) type `showrev -a`.