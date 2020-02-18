---
title: Linux Crontab 计划任务使用与说明
date: 2017-09-13 15:16:17
tags: [Linux]
categories: [Linux]
---


本文参考：[Linux定时任务Crontab命令详解](http://www.cnblogs.com/intval/p/5763929.html)

linux 系统则是由 cron (crond) 这个系统服务来控制的。Linux 系统上面原本就有非常多的计划性工作，因此这个系统服务是默认启动的。另 外, 由于使用者自己也可以设置计划任务，所以， Linux 系统也提供了使用者控制计划任务的命令 :crontab 命令。

### crond简介

crond 是linux下用来周期性的执行某种任务或等待处理某些事件的一个守护进程，与windows下的计划任务类似，当安装完成操作系统后，默认会安装此服务 工具，并且会自动启动crond进程，crond进程每分钟会定期检查是否有要执行的任务，如果有要执行的任务，则自动执行该任务。

Linux下的任务调度分为两类，**系统任务调度**和**用户任务调度**。

**系统任务调度**：系统周期性所要执行的工作，比如写缓存数据到硬盘、日志清理等。在`/etc`目录下有一个crontab文件，这个就是系统任务调度的配置文件。

/etc/crontab文件包括下面几行：  
```
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
MAILTO=root
HOME=/
```

前四行是用来配置crond任务运行的环境变量:  
第一行SHELL变量指定了系统要使用哪个shell，这里是bash，  
第二行PATH变量指定了系统执行命令的路径，  
第三行MAILTO变量指定了crond的任务执行信息将通过电子邮件发送给root用户，如果MAILTO变量的值为空，则表示不发送任务 执行信息给用户，  
第四行的HOME变量指定了在执行命令或者脚本时使用的主目录。  


**用户任务调度**：用户定期要执行的工作，比如用户数据备份、定时邮件提醒等。用户可以使用 `crontab` 工具来定制自己的计划任务。所有用户定义的crontab 文件都被保存在 `/var/spool/cron`目录中。其文件名与用户名一致。

用户新建、修改计划任务直接运行命令`crontab -e`



### crontab文件的含义：

所有用户crontab文件存放的目录,以用户名命名

用户所建立的crontab文件中，每一行都代表一项任务，每行的每个字段代表一项设置，它的格式共分为六个字段，前五段是时间设定段，第六段是要执行的命令段，格式如下：  
`minute hour day month week command`

其中：  
`minute`： 表示分钟，可以是从0到59之间的任何整数。  
`hour`：表示小时，可以是从0到23之间的任何整数。  
`day`：表示日期，可以是从1到31之间的任何整数。  
`month`：表示月份，可以是从1到12之间的任何整数。  
`week`：表示星期几，可以是从0到7之间的任何整数，这里的0或7代表星期日。  
`command`：要执行的命令，可以是系统命令，也可以是自己编写的脚本文件。  

![Crontab格式](/sourcepictures/2017/09/13/crontab.png)

在以上各个字段中，还可以使用以下特殊字符：  
`星号（*）`：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。  
`逗号（,）`：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”  
`中杠（-）`：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”  
`正斜线（/）`：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。

### crond服务

安装crontab：  

`yum install crontabs`

服务操作说明：  
```bash
/sbin/service crond start //启动服务  
/sbin/service crond stop //关闭服务
/sbin/service crond restart //重启服务
/sbin/service crond reload //重新载入配置
/sbin/service crond status //检测crontab是否启动成功
```

查看crontab服务是否已设置为开机启动，执行命令：  
`chkconfig  --list`

加入开机自动启动：
`chkconfig –level 35 crond on`

### Ubuntu中
在Ubuntu中服务名为cron，设置命令为crontab


### crontab 日志
CentOS: `tail -f /var/log/cron`

Ubuntu: `tail -f /var/log/cron.log`

注：Ubuntu 默认没有开启cron log.

在`/etc/rsyslog.d/50-default.conf`文件中取消`#cron.* /var/log/cron.log`注释符号`#`， 并重启rsyslog：`sudo service rsyslog restart`

### 输出

执行任务过程中会往屏幕输出内容, 当你执行的是个脚本文件时,你可以把文件的输出重定向到某个文件中.

`cd /home/wang && ./temp.sh >temp.log`

`cd /home/wang && ./temp.sh >>temp.log`

### crontab命令详解

通过crontab 命令，我们可以在固定的间隔时间执行指定的系统指令或 shell script脚本。时间间隔的单位可以是分钟、小时、日、月、周及以上的任意组合。这个命令非常设合周期性的日志分析或数据备份等工作。

**命令格式**：  
`crontab [-u user] file`  
`crontab [-u user] [ -e | -l | -r ]`

**命令参数**：

`-u user`：用来设定某个用户的crontab服务，例如，“-u ixdba”表示设定ixdba用户的crontab服务，此参数一般有root用户来运行。  
`file`：file是命令文件的名字,表示将file做为crontab的任务列表文件并载入crontab。如果在命令行中没有指定这个文件，crontab命令将接受标准输入（键盘）上键入的命令，并将它们载入crontab。  
`-e`：编辑某个用户的crontab文件内容。如果不指定用户，则表示编辑当前用户的crontab文件。  
`-l`：显示某个用户的crontab文件内容，如果不指定用户，则表示显示当前用户的crontab文件内容。  
`-r`：从/var/spool/cron目录中删除某个用户的crontab文件，如果不指定用户，则默认删除当前用户的crontab文件。  
`-i`：在删除用户的crontab文件时给确认提示。

### 使用举例

使用实例

实例1：每1分钟执行一次command  
命令：  
`* * * * * command  `

实例2：每小时的第3和第15分钟执行  
命令：  
3,15 * * * * command  

实例3：在上午8点到11点的第3和第15分钟执行  
命令：  
3,15 8-11 * * * command  

实例4：每隔两天的上午8点到11点的第3和第15分钟执行  
命令：  
3,15 8-11 */2 * * command  

实例5：每个星期一的上午8点到11点的第3和第15分钟执行  
命令：  
3,15 8-11 * * 1 command  

实例6：每晚的21:30重启smb   
命令：  
30 21 * * * /etc/init.d/smb restart

实例7：每月1、10、22日的4 : 45重启smb   
命令：  
45 4 1,10,22 * * /etc/init.d/smb restart  

实例8：每周六、周日的1 : 10重启smb  
命令：  
10 1 * * 6,0 /etc/init.d/smb restart  

实例9：每天18 : 00至23 : 00之间每隔30分钟重启smb   
命令：  
0,30 18-23 * * * /etc/init.d/smb restart  

实例10：每星期六的晚上11 : 00 pm重启smb   
命令：  
0 23 * * 6 /etc/init.d/smb restart  

实例11：每一小时重启smb   
命令：  
`* */1 * * * /etc/init.d/smb restart  `

实例12：晚上11点到早上7点之间，每隔一小时重启smb   
命令：  
`* 23-7/1 * * * /etc/init.d/smb restart  `

实例13：每月的4号与每周一到周三的11点重启smb   
命令：  
0 11 4 * mon-wed /etc/init.d/smb restart  

实例14：一月一号的4点重启smb   
命令：  
0 4 1 jan * /etc/init.d/smb restart




