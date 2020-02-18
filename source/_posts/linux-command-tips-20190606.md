---
title: linux 常用命令的收集与整理
date: 2019-06-06 11:21:14
tags: [Linux]
categories: [Linux]
---


### SS 命令

`ss`命令用来显示处于活动状态的套接字信息。ss命令可以用来获取socket统计信息，它可以显示和netstat类似的内容。但ss的优势在于它能够显示更多更详细的有关TCP和连接状态的信息，而且比`netstat`更快速更高效。

当服务器的socket连接数量变得非常大时，无论是使用`netstat`命令还是直接`cat /proc/net/tcp`，执行速度都会很慢。可能你不会有切身的感受，但请相信我，当服务器维持的连接达到上万个的时候，使用`netstat`等于浪费 生命，而用ss才是节省时间。

天下武功唯快不破。`ss`快的秘诀在于，它利用到了TCP协议栈中tcp_diag。tcp_diag是一个用于分析统计的模块，可以获得Linux 内核中第一手的信息，这就确保了ss的快捷高效。当然，如果你的系统中没有tcp_diag，`ss`也可以正常运行，只是效率会变得稍慢。

选项：
```
-h：显示帮助信息；
-V：显示指令版本信息；
-n：不解析服务名称，以数字方式显示；
-a：显示所有的套接字；
-l：显示处于监听状态的套接字；
-o：显示计时器信息；
-m：显示套接字的内存使用情况；
-p：显示使用套接字的进程信息；
-i：显示内部的TCP信息；
-4：只显示ipv4的套接字；
-6：只显示ipv6的套接字；
-t：只显示tcp套接字；
-u：只显示udp套接字；
-d：只显示DCCP套接字；
-w：仅显示RAW套接字；
-x：仅显示UNIX域套接字。
```


列出所有打开的网络连接端口
`ss -l`

列出所有打开的网络连接端口，只显示TCP
`ss -lt`

---

### mkdir

`mkdir /data/site{a,b,c} -pv`
这个命令可以创建/data/sitea , /data/siteb, /data/sitec，使用`-p`可以递归创建多层目录


### bc
`bc` 命令是任意精度计算器语言，通常在linux下当计算器用，可以进行基本数学运算。

输入`bc`命令，进入交互模式，可以进行加、减、乘、除、指数（^）、模（%）运算，输入`quit`退出。

也可以通过管道符使用：
```
echo 15*5 |bc
```

```
# scale=2 设小数位，2 代表保留两位:
echo "scale=2;sqrt(8)"|bc

#  ibase 和 obase 来其它进制的运算
abc = 11000000
echo "obase=10;ibase=2;$abc" | bc

abc=8
echo "obase=2;$abc" | bc

```


### apt-get 只升级单个包

只需要做`apt-get install --only-upgrade <packagename>`。这将仅升级该单个软件包，并且仅在已安装时才升级。


### grep 过滤空行和注释行

在查看配置文件时，我们希望过滤到注释行和大量的空行，只显示有效的配置信息，这时我们可以使用grep

grep的排除选项为 -v

1. 去掉`#`号开头的行

```
grep -v "^#"  filename
```


2. 去掉空行

```
grep -v "^$" filename 
```

3. 去掉空行及`#`开头的行

```
grep -v "^#" |grep -v "^$"

例如：
more  /etc/zabbix/zabbix_server.conf |grep -v "^#"|grep -v "^$"
```


### WC命令

Linux wc命令用于计算字数。

利用wc指令我们可以计算文件的Byte数、字数、或是列数，若不指定文件名称、或是所给予的文件名为"-"，则wc指令会从标准输入设备读取数据。

```
-c或--bytes或--chars 只显示Bytes数。
-l或--lines 只显示行数。
-w或--words 只显示字数。
```

例如： ps aux |wc -l

### dig命令

dig 命令主要用来从 DNS 域名服务器查询主机地址信息

dig 命令默认的输出信息比较丰富，大概可以分为 5 个部分。
第一部分显示 dig 命令的版本和输入的参数。
第二部分显示服务返回的一些技术详情，比较重要的是 status。如果 status 的值为 NOERROR 则说明本次查询成功结束。
第三部分中的 "QUESTION SECTION" 显示我们要查询的域名。
第四部分的 "ANSWER SECTION" 是查询到的结果。
第五部分则是本次查询的一些统计信息，比如用了多长时间，查询了哪个 DNS 服务器，在什么时间进行的查询等等。

### dhclient
获取动态ip
