---
title: Kali 启用自动登录
date: 2019-11-22 09:37:24
tags: [Kali]
categories: [Linux]
---



### kali2019.3

Kali Linux use `GDM3` as a default Display Manager. First confirm that your Kali Linux is set to use `GDM3` as a default display manager: 

```
# cat /etc/X11/default-display-manager
/usr/sbin/gdm3
```

Next, edit `/etc/gdm3/daemon.conf`

```
AutomaticLoginEnable = true
AutomaticLogin = root
```


### kali2019.4

```

cat /etc/X11/default-display-manager
/usr/sbin/lightdm

vim /etc/lightdm/lightdm.conf 

autologin-guest=True
autologin-user=root
autologin-user-timeout=0

```
