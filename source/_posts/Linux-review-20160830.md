---
title: Linux 温习(一)
date: 2016-08-30 22:49:02
tags: Linux
categories: [Linux]
---

## Linux X11 是什么？


***
## Linux Shell 常用快捷键

使用Tab键来进行命令补全，补全目录，补全命令参数

Ctrl+c键来强行终止当前程序




|快捷键  | 功能|
|--------|-----|
|Ctrl+c  |键来强行终止当前程序|
|Ctrl+d  |键盘输入结束或退出终端|
|Ctrl+s  |暂定当前程序，暂停后按下任意键恢复运行|
|Ctrl+z  |将当前程序放到后台运行，恢复到前台为命令<font color=Crimson size=3>fg</font>|
|Ctrl+a  |将光标移至输入行头，相当于<font color=Crimson size=3>Home</font>键|
|Ctrl+e  |将光标移至输入行末，相当于<font color=Crimson size=3>End</font>键|
|Ctrl+k  |删除从光标所在位置到行末|
|Alt+Backspace  |向前删除一个单词|
|Shift+PgUp  |将终端显示向上滚动|
|Shift+PgDn  |将终端显示向下滚动|



## Shell 通配符

Linux在创建文件的时候，如果需要一次性创建多个文件，比如：“love\_1\_linux.txt，love\_2\_linux.txt，... love\_10\_linux.txt”。可以使用：
    touch love\_{1..10}\_linux.txt



|字符  |含义 |
|------|-----|
|*   |匹配 0 或多个字符|
|?   |匹配任意一个字符|
|[list]  |匹配 list 中的任意单一字符|
|[!list]     |匹配 除list 中的任意单一字符以外的字符|
|[c1-c2]     |匹配 c1-c2 中的任意单一字符 如：[0-9] [a-z]|
|{string1,string2,...}   |匹配 sring1 或 string2 (或更多)其一字符串|
|{c2..c2}    |匹配 c1-c2 中全部字符 如{1..10}|



## 用户与文件权限管理

### 查看用户
who am i
whoami

### 添加用户
sudo adduser lilei
su <user> 可以切换到用户user
sudo <cmd> 可以以特权级别运行命令，需要当前用户属于sudo组，并输入密码
su -l <user> 命令也是切换用户，同时环境变量也会变成目标用户的环境变量

### 用户组
1.查看用户组 groups
e.g: groups username
显示结果冒号前的为用户名，冒号后为用户组。在新建用户时如果没有指定用户组，会默认创建一个同名的用户组。
2.查看/etc/group文件
    cat /etc/group |sort
group文件格式：group_name：password：GID：user_list
x表示密码不可见
3.添加用户到sudo组
    sudo usermod -G sudo username
4.删除用户
    sudo deluser username \-\-remove\-home
\-\-remove\-home表示同时删除home目录


## Linux文件权限

## 查看权限
ls -l
借图，致谢：实验楼
![File_name_type_attr](/sourcepictures/20160831/file_name_type_attr.png)
![file_dwrx](/sourcepictures/20160831/file_dwrx.png)


ls -Al 显示所有文件详细信息，linux中隐藏文件以‘.’开头
ls -AsSh 显示所有文件大小，并以普通人能看懂的方式呈现，s显示文件大小，S表示按文件大小排序

## 目录操作
～ 当前用户home目录
pwd
touch filename
mkdir
cp sourcefile destfile
cp -r 复制目录
rm file_or_fold 强制-f,递归-r,删除文件夹不行用rmdir
mv
cat 打印文件内容到标准终端，正序显示，显示行号-n
tac 打印文件内容到标准终端，倒序显示
nl  添加行号并打印，比cat -n 更专业
more  使用Enter滚动一行，使用Space滚动翻屏，使用h显示帮助，q退出
less
head  使用-n确定行数，默认10行
tail
file  查看文件类型
nohub 进程后台执行
