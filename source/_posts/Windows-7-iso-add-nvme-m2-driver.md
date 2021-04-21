---
title: Windows 7 原版镜像添加nvme m2驱动
date: 2020-08-16 21:09:23
tags: [Windows]
categories:
---

首先，如果不是可以自己制作ISO的话，可以尝试it天空的，万能驱动7，已经集成了NVME、USB3驱动和一些更新。

### 需要的软件  
Dism++


###  注意事项  
你的硬盘应该能在BIOS中看到，如果看不到，我的方法可能不行

如果PE没有发现USB3或NVME设备（即PE不支持），那么万能驱动7也发现不了。

万能驱动7的流程：  
```
进入PE->恢复待部署的系统到系统盘->运行万能驱动7->导入驱动到系统驱动库->重启->开始系统部署->部署过程中系统会自动安装导入的驱动->自动重启进入系统，
```





### 以下操作可行

个人的nvme盘是m2，西部数据SN730

建议在优启通PE中进行修改wim操作，使用`dism++`挂载映像，在驱动管理中添加如下驱动：

```
dfsd
```

在更新管理中，安装如下补丁：  

```
Windows6.1-KB2990941-v3-x64.msu
Windows6.1-KB3087873-v2-x64.msu
Windows6.1-KB2534111-x64.msu
Windows6.1-KB2999226-x64.msu
```

网上有很多说只需两个，但是这里有四个，也建议如此

wim修改完毕后，保存wim。



接下来，使用WinNTSetup安装器，进行系统安装。  首先，选择wim文件位置，选择引导驱动器的位置，也就是要安装的系统盘位置（SSD盘），选择完毕后三个指示符应该均为绿色，选择安装驱动器的位置，和上一步一样即可，同一个盘符。

注意：SSD系统盘要使用MBR格式，如果是GPT格式，那需要DiskGinus来修改。大致操作，如果SSD没有重要文件，可以先把SSD所有分区删除，然后保存。在SSD上点击右键，将分区表类型转换为MBR格式；然后点击新建分区（不是快速分区），直接下一步、完成即可；点击保存更改，会提示格式化，确定；即可。

继续WinNTSetup安装器，左下角选项，选择要安装的版本



下面谈一下wim封装ISO：  

网上有很多方法，都是介绍ultraISO直接替换修改后的install.wim，但是，经过多次尝试，总是失败。





另外，对于Win10的安装ISO来说，其PE环境是同时支持esd和wim安装的，但win7的安装PE环境不支持esd，因为win7时代esd这个格式还没诞生嘛，所以你把转完之后的ESD直接放到Win7的ISO里肯定是用不了的，那咋办呢？  
其实很简单，你找一个win10原版ISO，把里面的install.wim文件删掉，把win7的自己修改后的install.esd文件放进去就行了，这样虽然安装界面是win10的，但装出来的系统还是win7，同时还避免了PE环境不支持USB3.0的问题。
说简单点，就是自己杂交一个安装ISO，安装环境是Win10的，但install.esd是Win7的就好了，我自己测试过，没有问题。

可以将驱动封装到install.wim，然后在杂交生成ISO，既解决了USB3.0问题，又能解决驱动问题。



推荐阅读：https://www.jianshu.com/p/a93253560d78







### 另外一种方法

[How to prepare the Windows 7 installation ISO image with USB3.0 driver and NVM Express (NVMe) driver](https://support.lenovo.com/hk/en/solutions/ht103541/)

```

NOTE: It is not recommended to install the NVMe hotfix as a step in the task sequence, please follow the steps below for adding it to the install wim for best results.
1. Install the latest Windows ADK for Windows 8.1 update.
2. Create five local folders at C:\temp and name them as src, mount, winremount, hotfix, and drivers.
3. Copy the setup files from the DVD drive or the mounted Windows 7 installation ISO image to C:\temp\src.
4. Download and install the following NVMe driver for Windows 7 from Microsoft Web site, and then copy the hotfix.msu or hotfix.cab file to C:\temp\hotfix..
Update to add native driver support in NVM Express in Windows 7 and Windows Server 2008 R2 li>
5. If you want to install the Windows 7 operating system using a USB flash drive, copy the USB 3.0 driver files to C:\temp\drivers.
6. Click Start, and type deployment. Right-click Deployment and Imaging Tools Environment and then select Run as administrator.
7. Copy the hotfixes and drivers to the boot.wim file, and then update the sources folder by running the following Deployment Image Servicing and Management (DISM) commands: dism /Mount-Image /ImageFile:c:\temp\src\sources\boot.wim /Index:1 /MountDir:c:\temp\mount

dism /Image:C:\temp\mount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\mount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Unmount-Image /MountDir:C:\temp\mount /Commit
dism /Mount-Image /ImageFile:c:\temp\src\sources\boot.wim /Index:2 /MountDir:c:\temp\mount
dism /Image:C:\temp\mount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\mount /Add-Driver /Driver:c:\temp\drivers /Recurse

Sort the files at C:\temp\mount\sources by date, and then copy the updated files to C:\temp\src\sources (ensure that C:\temp\mount\sources is closed before running the following DISM command). 

dism /Unmount-Image /MountDir:C:\temp\mount /commit

8. Obtain the index from the Install.wim file by running the following command, and then check every index to see how many indexes have to be updated.

dism /Get-WimInfo /WimFile:c:\temp\src\sources\install.wim

9. Copy the hotfixes and drivers to the install.wim file and the winre.wim file by running the following commands:

dism /Mount-Image /ImageFile:c:\temp\src\sources\install.wim /Index:1 /MountDir:c:\temp\mount
dism /Image:C:\temp\mount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\mount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Mount-Image /ImageFile:c:\temp\mount\windows\system32\recovery\winre.wim /Index:1 /MountDir:c:\temp\winremount
dism /Image:C:\temp\winremount/Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\winremount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Unmount-Wim /MountDir:C:\temp\winremount /Commit
dism /Unmount-Wim /MountDir:C:\temp\mount /Commit 

Note: In step 8, if you find multiple indexes have to be updated, update them one by one (only the index number of the first line needs to be updated). For example, if there are four indexes, run the following commands: dism /Mount-Image /ImageFile:c:\temp\src\sources\install.wim /Index:1 /MountDir:c:\temp\mount

dism /Image:C:\temp\mount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\mount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Mount-Image /ImageFile:c:\temp\mount\windows\system32\recovery\winre.wim /Index:1 /MountDir:c:\temp\winremount
dism /Image:C:\temp\winremount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\winremount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Unmount-Wim /MountDir:C:\temp\winremount /Commit
dism /Unmount-Wim /MountDir:C:\temp\mount /Commit
dism /Mount-Image /ImageFile:c:\temp\src\sources\install.wim /Index:2 /MountDir:c:\temp\mount
dism /Image:C:\temp\mount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\mount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Mount-Image /ImageFile:c:\temp\mount\windows\system32\recovery\winre.wim /Index:1 /MountDir:c:\temp\winremount
dism /Image:C:\temp\winremount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\winremount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Unmount-Wim /MountDir:C:\temp\winremount /Commit
dism /Unmount-Wim /MountDir:C:\temp\mount /Commit
dism /Mount-Image /ImageFile:c:\temp\src\sources\install.wim /Index:3 /MountDir:c:\temp\mount
dism /Image:C:\temp\mount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\mount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Mount-Image /ImageFile:c:\temp\mount\windows\system32\recovery\winre.wim /Index:1 /MountDir:c:\temp\winremount
dism /Image:C:\temp\winremount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\winremount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Unmount-Wim /MountDir:C:\temp\winremount /Commit
dism /Unmount-Wim /MountDir:C:\temp\mount /Commit
dism /Mount-Image /ImageFile:c:\temp\src\sources\install.wim /Index:4 /MountDir:c:\temp\mount
dism /Image:C:\temp\mount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\mount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Mount-Image /ImageFile:c:\temp\mount\windows\system32\recovery\winre.wim /Index:1 /MountDir:c:\temp\winremount
dism /Image:C:\temp\winremount /Add-Package /PackagePath:c:\temp\hotfix
dism /Image:C:\temp\winremount /Add-Driver /Driver:c:\temp\drivers /Recurse
dism /Unmount-Wim /MountDir:C:\temp\winremount /Commit
dism /Unmount-Wim /MountDir:C:\temp\mount /Commit

10. Create an ISO file, and then rename the label by running the following oscdimg commands:
For Legacy BIOS Boot mode:
oscdimg -LTEST -m -u2 -bC:\temp\src\boot\etfsboot.com C:\temp\src C:\temp\Win7.NVME.ISO

For Legacy and UEFI BIOS multiple Boot mode:
oscdimg -LTEST -m -u2 -bootdata:2#p0,e,bC:\temp\src\boot\etfsboot.com#pEF,e,bC:\temp\src\efi\microsoft\boot\efisys.bin C:\temp\src C:\temp\Win7.NVME.ISO

11. Use the ISO file to create a bootable USB flash drive with FAT32 or a bootable DVD.
Note: If the ISO file is larger than 4.7 GB, use a dual-layer DVD to burn the ISO file.

12. For UEFI boot (only applicable for the 64-bit version of Windows 7 operating system or higher), check if the BOOTX64.EFI file is available at EFI\BOOT\ on the USB flash drive. If not, do the following:
a. Copy the bootmgfw.efi file from C:\Windows\Boot\EFI to \EFI\BOOT\ on the USB flash drive.
b. Rename the file to BOOTX64.EFI.
```



### 谈一谈Win10的封装





```
关于优化我这里先说一下几个坑
1.对于1903及以上的系统，不要禁用windows错误报告服务，即Windows Error Reporting Service。这个服务一旦禁用，打开Widows设置会出现数组越界的BUG或者闪退，甚至可能严重影响系统性能。
2.建议不要禁用系统更新服务，即Windows Update服务。如果禁用了，会出现很多问题，比如无法安装.net3.5，无法使用数字激活工具激活系统，无法使用应用商店下载APP等等。
3.不要禁用Windows防火墙服务，即Windows Firewall服务。如果禁用了，就无法共享打印机。
4.优化《解除程序文件风险警告》后会造成IE自带下载器下载的程序，无法在下载器里直接运行。
5.对于1903及以上的系统，优化《禁止商店自动下载更新》后，无法使用新建账户的方式登录系统，会卡在OOBE界面，但不影响内置管理员方式登录系统。所以，如果不是内置管理员封装的系统就不要优化此项。
```









