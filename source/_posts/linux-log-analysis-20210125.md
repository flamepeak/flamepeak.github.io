---
title: linux登录日志分析
date: 2021-01-25 12:00:28
tags: Linux
categories: [Linux]
---



Linux is very good at keeping logs of everything that goes on your system. Quite naturally, it also stores logs about login and login attempts. The login information is stored in three places:

- /var/log/wtmp – Logs of last login sessions
- /var/run/utmp – Logs of the current login sessions
- /var/log/btmp – Logs of the bad login attempts

**Difference between pts and tty?**

You may see `pts` and `tty` when you use the who command, do you know what it is? how they are different?

- A tty is a native terminal device (It could be a server/system console).
- A pty (pseudo terminal device) is a terminal device which is emulated by an other program such as putty, xterm or screen, etc,.
pts/0 means the server was accessed via SSH.





#### last

   The last command fetch the details from the `/var/log/wtmp` file and it displays the list of users logged-in and logged-out from system.

   If the file has more data then use `less` or `more` command to view the information page wise.

   ```
   # last -15
   
   daygeek  pts/0        192.168.1.6      Wed Apr  3 00:02   still logged in   
   reboot   system boot  3.10.0-957.el7.x Tue Apr  2 23:50 - 00:03  (00:13)    
   daygeek  pts/0        192.168.1.6      Tue Apr  2 06:02 - 06:02  (00:00)    
   reboot   system boot  3.10.0-957.el7.x Tue Apr  2 06:00 - 00:03  (18:03)    
   ```

   Check Certain user
   ```
   last <username>
   ```

   


#### lastb
View all the bad login attempts  
```
last -f /var/log/btmp
```

or you can use the lastb command:  
```
lastb
```





### 一：Linux痕迹清理

清理命令记录：

(1)仅清理当前用户： `history -c`

或者，编辑history记录文件，删除部分不想被保存的历史命令。

```
vim ~/.bash_history
```

或者，kill history

```
kill -9 $$       
```



(2)使系统不再保存命令记录：`vi /etc/profile`，找到HISTSIZE这个值，修改为0

(3)删除记录，至少删除下面几项日志

删除登录失败记录：`echo > /var/log/btmp `
删除登录成功记录：`echo > /var/log/wtmp` （此时执行last命令就会发现没有记录） 
删除日志记录：`echo > /var/log/secure`

删除：`/var/log/message`

删除：`/var/log/lastlog`

日志说明：

```
/var/log/message 系统启动后的信息和错误日志，是Red Hat Linux中最常用的日志之一
/var/log/secure 与安全相关的日志信息
/var/log/maillog 与邮件相关的日志信息
/var/log/cron 与定时任务相关的日志信息
/var/log/spooler 与UUCP和news设备相关的日志信息
/var/log/boot.log 守护进程启动和停止相关的日志消息
/var/log/btmp – 记录所有失败登录信息  命令:lastb
```



### 二、Linux文件安全删除工具

（1）shred命令
实现安全的从硬盘上擦除数据，默认覆盖3次，通过 -n指定数据覆盖次数。

```
[root@centos]# shred -f -u -z -v -n 8 1.txt 
shred: 1.txt: pass 1/9 (random)...
shred: 1.txt: pass 2/9 (ffffff)...
shred: 1.txt: pass 3/9 (aaaaaa)...
shred: 1.txt: pass 4/9 (random)...
shred: 1.txt: pass 5/9 (000000)...
shred: 1.txt: pass 6/9 (random)...
shred: 1.txt: pass 7/9 (555555)...
shred: 1.txt: pass 8/9 (random)...
shred: 1.txt: pass 9/9 (000000)...
shred: 1.txt: removing
shred: 1.txt: renamed to 00000
shred: 00000: renamed to 0000
shred: 0000: renamed to 000
shred: 000: renamed to 00
shred: 00: renamed to 0
shred: 1.txt: removed
```

（2）dd命令
可用于安全地清除硬盘或者分区的内容。

```
dd if=/dev/zero of=要删除的文件 bs=大小 count=写入的次数
```

（3）wipe
Wipe 使用特殊的模式来重复地写文件，从磁性介质中安全擦除文件。

```
wipe filename
```

（4）Secure-Delete
Secure-Delete 是一组工具集合，提供srm、smem、sfill、sswap，4个安全删除文件的命令行工具。

```
srm filename
sfill filename
sswap /dev/sda1
smem
```



### 三、隐藏远程SSH登陆记录

隐身登录系统，不会被w、who、last等指令检测到。

```
ssh -T root@192.168.0.1 /bin/bash -i
```

不记录ssh公钥在本地.ssh目录中

```
ssh -o UserKnownHostsFile=/dev/null -T user@host /bin/bash –i
```

