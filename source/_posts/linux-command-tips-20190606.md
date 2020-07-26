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

grep 命令用于查找文件里符合条件的字符串。

例如：  
```bash
root@kali:~/Downloads# grep def psexec.py
    ...
    def connectPipe(self):                                                                                                
    def __init__(self, transport, pipe, permisssions):                                           
    def run(self):
    ...                                                                                                               
```

```bash
从文件内容查找匹配指定字符串的行：
$ grep "被查找的字符串" 文件名

例子：在当前目录里第一级文件夹中寻找包含指定字符串的 .in 文件
grep "thermcontact" /.in

从文件内容查找与正则表达式匹配的行：
$ grep –e "正则表达式" 文件名

查找时不区分大小写：
$ grep –i "被查找的字符串" 文件名

查找匹配的行数：
$ grep -c "被查找的字符串" 文件名

从文件内容查找不匹配指定字符串的行：
$ grep –v "被查找的字符串" 文件名

从根目录开始查找所有扩展名为 .log 的文本文件，并找出包含 "ERROR" 的行：
$ find / -type f -name "*.log" | xargs grep "ERROR"

例子：从当前目录开始查找所有扩展名为 .in 的文本文件，并找出包含 "thermcontact" 的行：
find . -name "*.in" | xargs grep "thermcontact"
```




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

```bash
从指定的 DNS 服务器上查询，命令：@
如果某网络有自己的dns服务，就需要从指定服务器查询
dig @8.8.8.8 abc.filterinto.com


区域传输记录(必须是询问一台授权的服务器)，命令：axfr
dig axfr @server zx.xmgd.com

用 dig 查看反向解析
dig -x 210.52.83.228 @server
```



### dhclient
获取动态ip



### stty

```bash
stty [ -a ] [ -g ] [ Options ]   stty(set tty)命令用于显示和修改当前注册的终端的属性。

UNIX系统为键盘的输入和终端的输出提供了重要的控制手段，可以通过stty命令对特定终端或通信线路设置选项。 

在stty中相应选项前冠以负号（-），该选项被清除；如果无负号，该选项被设置。

stty -a #将所有选项设置的当前状态写到标准输出中
old_stty_settings=`stty -g`　#保存当前设置
stty "$old_stty_settings"　#恢复当前设置
stty -echo #禁止回显，当您在键盘上输入时，并不出现在屏幕上
stty echo #打开回显
stty raw #设置原始输入
stty -raw #关闭原始输入
stty igncr #开启忽略回车符
stty -igncr #关闭忽略回车符
```





RAW模式与非RAW模式  
RAW模式简单的来说，就是发送端发动的二进制码原封不动的被接收端接收。

非RAW模式  
非RAW模式下，系统会对串口收到的数据中某些具有特殊意义的字符或组合进行转义。这种 工作模式的典型是在Linux下使用 minicom 配置交换机或串口登录其他Linux系统。



### sed
sed 是一种在线编辑器，它一次处理一行内容。处理时，把当前处理的行存储在临时缓冲区中，称为“模式空间”（pattern space），接着用sed命令处理缓冲区中的内容，处理完成后，把缓冲区的内容送往屏幕。接着处理下一行，这样不断重复，直到文件末尾。文件内容并没有 改变，除非你使用重定向存储输出。Sed主要用来自动编辑一个或多个文件；简化对文件的反复操作；编写转换程序等。

```bash
[root@www ~]# sed [-nefr] [动作]
选项与参数：
-n ：使用安静(silent)模式。在一般 sed 的用法中，所有来自 STDIN 的数据一般都会被列出到终端上。但如果加上 -n 参数后，则只有经过sed 特殊处理的那一行(或者动作)才会被列出来。
-e ：直接在命令列模式上进行 sed 的动作编辑；
-f ：直接将 sed 的动作写在一个文件内， -f filename 则可以运行 filename 内的 sed 动作；
-r ：sed 的动作支持的是延伸型正规表示法的语法。(默认是基础正规表示法语法)
-i ：直接修改读取的文件内容，而不是输出到终端。

动作说明： [n1[,n2]]function
n1, n2 ：不见得会存在，一般代表『选择进行动作的行数』，举例来说，如果我的动作是需要在 10 到 20 行之间进行的，则『 10,20[动作行为] 』

function：
a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～
c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！
d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；
i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；
p ：列印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～
s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是啦！
```


最常用操作：

数据的搜寻并替换
除了整行的处理模式之外， sed 还可以用行为单位进行部分数据的搜寻并取代。基本上 sed 的搜寻与替代的与 vi 相当的类似！他有点像这样：

```bash
sed 's/要被取代的字串/新的字串/g'
```







