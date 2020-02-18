---
title: Linux 使用技巧 命令
date: 2016-12-30 14:28:22
tags: [Linux, Python]
categories: [Linux]
---


### 获取进程的完整路径
Linux在启动一个进程时，系统会在/proc下创建一个以PID命名的文件夹，在这个文件夹下面有进程的信息，其中名为exe的文件记录了绝对路径。
```
ps -A

ll /proc/PID
```

***

### 查看发行版

```bash
cat /etc/issue
/etc/issue文件通常包括系统的一段短说明或欢迎信息.内容由系统管理员确定。

cat /etc/*-release
```


### 查看内核版本
`uname -a`

### 查看已经安装的程序:
```bash
dpkg -l
rpm -qa
```

***

### passwd和shadow
大多linux系统的密码都和`/etc/passwd`和`/etc/shadow`这两个配置文件息息相关。passwd里面储存了用户，shadow里面是密码的hash。出于安全考虑passwd是全用户可读，root可写的。shadow是仅root可读写的。

这里是一个典型的passwd文件
```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/bin/sh
man:x:6:12:man:/var/cache/man:/bin/sh
lp:x:7:7:lp:/var/spool/lpd:/bin/sh
mail:x:8:8:mail:/var/mail:/bin/sh
news:x:9:9:news:/var/spool/news:/bin/sh
uucp:x:10:10:uucp:/var/spool/uucp:/bin/sh
proxy:x:13:13:proxy:/bin:/bin/sh
www-data:x:33:33:www-data:/var/www:/bin/sh
backup:x:34:34:backup:/var/backups:/bin/sh
list:x:38:38:Mailing List Manager:/var/list:/bin/sh
irc:x:39:39:ircd:/var/run/ircd:/bin/sh
nobody:x:65534:65534:nobody:/nonexistent:/bin/sh
ibuuid:x:100:101::/var/lib/libuuid:/bin/sh
syslog:x:101:103::/home/syslog:/bin/false
sshd:x:104:65534::/var/run/sshd:/usr/sbin/nologin
```

`passwd`由冒号分割，第一列是用户名，第二列是密码，`x`代表密码hash被放在`shadow`里面了（这样非root就看不到了）。而`shadow`里面最重要的就是密码的hash
```
root:$6$URgq7sJf$4x8e9ntqTwAPIubi9YLxLQ2mZTTZKnGz0g/wWzOdPB5eGuz.S5iRtFdvfFd9VIVEWouiodB/hh9BYOLgAD8u5/:16902:0:99999:7:::
daemon:*:15730:0:99999:7:::
bin:*:15730:0:99999:7:::
sys:*:15730:0:99999:7:::
sync:*:15730:0:99999:7:::
games:*:15730:0:99999:7:::
man:*:15730:0:99999:7:::
lp:*:15730:0:99999:7:::
mail:*:15730:0:99999:7:::
news:*:15730:0:99999:7:::
uucp:*:15730:0:99999:7:::
proxy:*:15730:0:99999:7:::
www-data:*:15730:0:99999:7:::
backup:*:15730:0:99999:7:::
list:*:15730:0:99999:7:::
irc:*:15730:0:99999:7:::
gnats:*:15730:0:99999:7:::
nobody:*:15730:0:99999:7:::
libuuid:!:15730:0:99999:7:::
syslog:*:15730:0:99999:7:::
mysql:!:15730:0:99999:7:::
dovecot:*:15730:0:99999:7:::
sshd:*:15730:0:99999:7:::
postfix:*:15730:0:99999:7:::
```

如果passwd可写，我们就可以把root的密码字段(x)替换成一个已知密码的hash（比如本机shadow里面的root密码hash），这样系统在验证密码时以passwd的为准，密码就已知了。如果shadow可读，我们可以读走root的hash，然后用hashcat或者john暴力破解之。

***

### Python模拟一个终端设备
有了（疑似）root密码怎么办？你一定想ssh登陆。然而ssh很可能禁止root登陆，或是防火墙规则将你排除在外了。返回来想，我们不是有一个低权shell了吗？找个办法再上面“输入”密码就好了。显然，直接在低权shell里面用sudo是不奏效的。这是因为出于安全考虑，linux要求用户必须从 **终端设备（tty）** 中输入密码，而不是标准输入（stdin）。换句话说，`sudo`在你输入密码的时候本质上是读取了键盘，而不是bash里面输入的字符。因此为了能够输入密码，我们必须模拟一个终端设备。python就有这样的功能。在shell里面输入：

`python -c 'import pty;pty.spawn("/bin/sh")'`
就用python建立了一个虚拟终端，然后就可以使用sudo等等命令了。

```bash
python -c 'import pty;pty.spawn("/bin/sh")'
$ sudo su
sudo su
[sudo] password for www-data: 123456
Sorry, try again.
[sudo] password for www-data:
```

***

### Linux计划任务

系统内可能会有一些定时执行的任务，一般这些任务由`crontab`来管理，具有所属用户的权限。非root权限的用户是不可以列出root用户的计划任务的。但是`/etc/`内系统的计划任务可以被列出

`ls -l /etc/cron*`

***

### 网络与隐藏的服务
```bash
netstat -antup #查看各种网络服务
```

***

### netcat做个转发
```bash
mkfifo backpipe
nc -l 8082 0<backpipe | nc remote_host 445 1>backpipe
```

***

### 
