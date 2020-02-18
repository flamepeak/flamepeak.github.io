---
title: 'Centos apache Permission denied:file permissions deny server access'
date: 2019-01-17 20:14:30
tags: [Web后端]
categories:
---

查错误日志， 发现apache启动时有提示：`[notice] SELinux policy enabled; httpd running as context unconfined_u:system_r:httpd_t:s0`

原来是SELinux的安全策略导致的。

临时方案：setenforce 0

永久方案：修改`/etc/sysconfig/selinux：SELINUX=disabled` 重启生效！



