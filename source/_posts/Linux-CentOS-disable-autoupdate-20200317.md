---
title: CentOS 7 禁用自动更新
date: 2020-03-17 09:35:12
tags: [Python]
categories: [Python]
---

PackageKit是基于RedHat的linux中的图形化软件升级工具，下面描述一下如何禁止和启用自动升级。

默认状态下是开启的。

```bash
# 查看状态
systemctl status packagekit

# 停用
systemctl stop packagekit

# 禁止自启动
systemctl mask packagekit
  Created symlink from /etc/systemd/system/packagekit.service to /dev/null.

# 卸载PackageKit
yum remove PackageKit*

```



```bash
# 安装PackageKit
yum install gnome-packagekit PackageKit-yum

# 自启动
systemctl unmask packagekit

#查看状态
systemctl status packagekit

#启动
systemctl start packagekit
```









systemctl的常见用法：

```
systemctl 提供了一组子命令来管理单个的 unit，其命令格式为：
systemctl [command] [unit]
command 主要有：
start：立刻启动后面接的 unit。
stop：立刻关闭后面接的 unit。
restart：立刻关闭后启动后面接的 unit，亦即执行 stop 再 start 的意思。
reload：不关闭 unit 的情况下，重新载入配置文件，让设置生效。
enable：设置下次开机时，后面接的 unit 会被启动。
disable：设置下次开机时，后面接的 unit 不会被启动。
status：目前后面接的这个 unit 的状态，会列出有没有正在执行、开机时是否启动等信息。
is-active：目前有没有正在运行中。
is-enable：开机时有没有默认要启用这个 unit。
kill ：不要被 kill 这个名字吓着了，它其实是向运行 unit 的进程发送信号。
show：列出 unit 的配置。
mask：注销 unit，注销后你就无法启动这个 unit 了。
unmask：取消对 unit 的注销。
```




