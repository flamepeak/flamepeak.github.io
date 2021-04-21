---
title: linux hostname与uname的联系与修改
date: 2021-02-22 09:27:39
tags: [Linux]
categories: [Linux]
---


`uname -a`是一个很常用的查看Linux信息的命令，包括处理器架构、主机名和内核版本。

主要参数如下：  

```
-s, (--kernel-name) - Prints the kernel name.
-n, (--nodename) - Prints the system’s node name (hostname). This is the name the system uses when communicating over the network. When used with the -n option, uname produces the same output as the hostname command.
-r, (--kernel-release) - Prints the kernel release.
-v, (--kernel-version) - Prints the kernel version.
-m, (--machine) - Prints the name of the machine’s hardware name.
-p, (--processor) - Prints the architecture of the processor.
-i, (--hardware-platform) - Prints the hardware platform.
-o, (--operating-system) - Print the name of the operating system. On Linux systems that is “GNU/Linux”
-a, (--all) - When the -a option is used, uname behaves the same as if the -snrvmo options have been given.
```

示例如下：

```
rui@localhost:~> uname -a
Linux linuxize.com 5.3.18-lp152.19-default #1 SMP Tue Jun 9 20:59:24 UTC 2020 (960cb00) x86_64 x86_64 x86_64 GNU/Linux
rui@localhost:~> uname
Linux
rui@localhost:~> uname -n
linuxize.com
lrui@localhost:~> uname -s
Linux
rui@localhost:~> uname -r
5.3.18-lp152.19-default
lrui@localhost:~> uname -p
x86_64
rui@localhost:~> uname -o
GNU/Linux
```

有意思的是`uname -n`,它显示的结果和`hostname`一致，如下：  
```
rui@localhost:~> uname -n
linuxize.com
rui@localhost:~> hostname
linuxize.com
```

在uname帮助文件中，这么描述“Prints the system’s node name (hostname). This is the name the system uses when communicating over the network. When used with the -n option, uname produces the same output as the hostname command.”


那么我们怎么修改hostname呢？

使用`hostnamectl`，如下：  
```
rui@localhost:~> hostnamectl --help
hostnamectl [OPTIONS...] COMMAND ...

Query or change system hostname.

  -h --help              Show this help
     --version           Show package version
     --no-ask-password   Do not prompt for password
  -H --host=[USER@]HOST  Operate on remote host
  -M --machine=CONTAINER Operate on local container
     --transient         Only set transient hostname
     --static            Only set static hostname
     --pretty            Only set pretty hostname

Commands:
  status                 Show current hostname settings
  set-hostname NAME      Set system hostname
  set-icon-name NAME     Set icon name for host
  set-chassis NAME       Set chassis type for host
  set-deployment NAME    Set deployment environment for host
  set-location NAME      Set location for host

```

效果如下：  
```
rui@localhost:~> hostnamectl set-hostname TECHTECH

rui@localhost:~> uname -a
Linux TECHTECH 5.3.18-lp152.19-default #1 SMP Tue Jun 9 20:59:24 UTC 2020 (960cb00) x86_64 x86_64 x86_64 GNU/Linux

rui@localhost:~> hostnamectl set-hostname linuxize.com

rui@localhost:~> uname -a
Linux linuxize.com 5.3.18-lp152.19-default #1 SMP Tue Jun 9 20:59:24 UTC 2020 (960cb00) x86_64 x86_64 x86_64 GNU/Linux

```




