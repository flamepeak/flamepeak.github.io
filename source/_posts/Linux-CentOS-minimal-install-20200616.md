---
title: CentOS 7 最小化安装遇到的问题
date: 2020-06-16 09:35:12
tags: [CentOS]
categories: [Linux]
---

### ifconfig not  found

```
ifconfig 在centos 最小化安装时，是不自带的
可以使用ip addr 查看ip
```

### 没有ip地址

```
ip addr发现ens33 没有inet 这个属性，那么就没法通过IP地址连接虚拟机。
查看ens33网卡的配置：vi /etc/sysconfig/network-scripts/ifcfg-ens33
从配置清单中可以发现 CentOS 7 默认是不启动网卡的（ONBOOT=no）。
把这一项改为YES（ONBOOT=yes）
重启网络服务： sudo service network restart
```



