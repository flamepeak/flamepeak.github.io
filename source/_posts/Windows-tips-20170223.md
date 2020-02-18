---
title: Windows 使用与问题解决
date: 2017-02-23 21:09:23
tags: [Windows]
categories:
---

### 虚拟机NAT模式 VPN
其实很多人连接不上VPN的原因是NAT模式是不支持VPN连接的。

### vbs脚本调试方法
vbs脚本调试可以使用visual studio, 在cmd中输入`wscript /x test.vbs`即可弹出调试选项，点击yes，程序会停在脚本的第一条语句上。

### WMIC的进程操作

#### 一、查询数据
1. 使用get查询
    ```
    wmic process get name,executablepath
    ```

2. 无条件查询
    ```
    wmic process list brief    
    wmic process list full
    ```

3. 加where条件查询 
    ```
    wmic process where name="qq.exe" get processid,executablepath,name
    ```

#### 二、创建进程
    ```
    wmic process call create  "c:\windows\system32\cmd.exe"
    ```

#### 三、结束进程
    ```
    wmic process where name="qq.exe" call terminate
    wmic process where name="qq.exe" delete
    ```
