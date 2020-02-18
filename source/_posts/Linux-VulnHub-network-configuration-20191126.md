---
title: Linux VulnHub VMware network configuration
date: 2019-11-26 08:38:54
tags: [Linux]
categories: [Linux]
---

本文参考了[https://blog.csdn.net/nzjdsds/article/details/82811791](https://blog.csdn.net/nzjdsds/article/details/82811791)，但是此文没有解决写权限的问题

在此详述以下个人问题解决过程


owa文件一般直接用Vmware打开，按流程导入即可

但是有时候会出现设置NAT网络，但是依然连不通的情况，这时候我们需要进入Linux恢复模式修改网卡设置

我们点击开启虚拟机，到开机的页面我们回车选择第二个Ubuntu的高级选项（如果启动有网络正常的话可以直接正常开机，如果网络不正常可以按下面步骤操作） (如果出不来这个选择界面，开机时按下shirt键)

![shift advanced options for ubuntu](/sourcepictures/2019/11/26/1.png)

进到高级选项，我们选择第二个Linux恢复模式(recovery mode)

![recovery mode](/sourcepictures/2019/11/26/2.png)

回车后会弹出选择界面，我们选择root一行回车，然后再次回车进入命令行模式

![root](/sourcepictures/2019/11/26/3.png)

一般来说，这种模式下都是只读模式，无法修改文件，可以执行以下命令，开启写权限

```bash
fsck -f -y /
mount --options remount,rw /
```

查看所有网卡：

```bash
ifconfig -a
```

![root](/sourcepictures/2019/11/26/4.png)

我的是ens33，然后继续输入命令 `vi /etc/network/interfaces` 修改网络配置文件

对于一些Linux版本，网络配置文件位于`/etc/netplan` 文件夹下，可以使用`ls /etc/netplan`查看文件夹内yaml文件名，然后使用`vi`修改此配置文件

![root](/sourcepictures/2019/11/26/5.png)

修改两个地方，改成你的网卡名称，然后ctrl+x保存，重启即可





对于采用HostOnly模式的虚拟机，可以参考此文 [VMware-Host only（主机模式）网络连接方式]( https://blog.csdn.net/qq_28817739/article/details/77542860 )，在虚拟网卡vmware network adapter VMnet1中修改子网ip地址




ubuntu12.04克隆后没有cannot find device eth0的问题

于是查看一下所有的网络信息

`ifconfig -a`
发现现实了eth2和eth3，并没有熟悉的eth0和eth1；
于是修改网络配置文件

`vi /etc/network/interfaces`

将`iface inet eth0 dhcp` 修改为`iface inet eth2 dhcp`

重启网卡

`/etc/init.d/networking restart`


而问题的原因在于：新克隆过来的机器网卡的mac地址是不同的，而以前的网卡信息依然保持在配置文件的那个中，如果找到这个文件将他删掉，让系统自动生成那么问题就能解决。

`vi /etc/udev/rules.d/70-persistent-net.rules`

可以看到咱们想要的东西 于是
```
rm /etc/udev/rules.d/70-persistent-net.rules
/etc/init.d/networking restart
```
同样可以解决该问题。



