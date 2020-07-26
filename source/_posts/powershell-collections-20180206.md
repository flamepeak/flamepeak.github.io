---
title: Powershell 使用方法收集
date: 2018-02-06 08:43:41
tags: [Network Security, powershell]
categories: [Network Security]
---

### powershell基础知识

1. [Windows PowerShell基本语法及常用命令](https://www.cnblogs.com/feng-zhizi/p/9935874.html)
	[powershell命令大全与命令简写](https://www.cnblogs.com/nightnine/p/5119653.html)
2. Test-Path, 可以用来测试一个目录或者文件是否存在；
	也可以用来测试一个路径到底是目录还是文件，通过添加参数 `-pathType` 来进行，参数可取 `container` 和 `leaf` 两个值
	```
	Test-Path C:\Scripts\Archive -pathType container
	```
	还可以，通过 `-include` 或 `-exclude` 参数来判断给定目录中是否包含指定的文件类型，或是只包含指定的文件类型:
	```
	Test-Path C:\Scripts\Archive\* -include Test*.ps1, Test*.vbs
	Test-Path C:\Scripts\Archive\* -exclude *.gif, *.jpg
	```

---

### 遇到的问题及解决办法

#### PowerShell 无法加载文件ps1，因为在此系统中禁止执行脚本

无法加载文件 XXX.ps1，因为在此系统中禁止执行脚本。有关详细信息，请参阅 "get-help about_signing"。

根据提示，运行get-help about-signing,找到解决办法

>  允许运行签名脚本
    首次在计算机上启动 Windows PowerShell 时，现用执行策略很可能是 Restricted（默认设置）。
    Restricted 策略不允许任何脚本运行。
    若要了解计算机上的现用执行策略，请键入：
        get-executionpolicy
    若要在本地计算机上运行您编写的未签名脚本和来自其他用户的签名脚本，请使用以下命令将计算机上的
    执行策略更改为 RemoteSigned：
        set-executionpolicy remotesigned

powershell脚本的执行策略有：`<Unrestricted> | <RemoteSigned> | <AllSigned> | <Restricted> | <Default> | <Bypass> | <Undefined>`

```powershell
set-executionpolicy unrestricted
```

关于没有系统管理员权限的情况下如何绕过：  
[哪些方法可以绕过PowerShell Execution Policy？](https://blog.csdn.net/qq_27446553/article/details/50577296)

####

---

### 获得当前系统已安装的程序列表

[获得当前系统已安装的程序列表](http://www.4hou.com/technology/10206.html)  

1. 使用powershell调用wmi
`Get-WmiObject -class Win32_Product`  
结果过滤：  
`Get-WmiObject -class Win32_Product | Select-Object -Property name`



2. 

