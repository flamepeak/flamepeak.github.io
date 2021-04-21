---
title: VS2019 在Windows 7 系统离线安装
date: 2021-01-20 10:27:55
tags: [windows]
categories: [Tools]
---

---



### 必要设置

WIN7安装VS2019需要更新两个补丁才能顺利安装，否则会闪退。  

KB4474419

KB4490628

补丁包下载地址：

https://www.catalog.update.microsoft.com/Search.aspx?q=KB4490628

https://www.catalog.update.microsoft.com/Search.aspx?q=KB4474419

根据自己系统版本选择对应文件下载即可







### 下载资源

首先，在可以上网的电脑上，下载Visual Studio web installer，

下载地址：https://visualstudio.microsoft.com/downloads



如果是`C++`开发环境：  

`vs_community.exe --layout c:\vslayout --add Microsoft.VisualStudio.Workload.NativeDesktop --includeRecommended --lang en-US`



如果是`.NET web  .NET desktop`开发环境：  

`vs_community.exe --layout c:\vslayout --add Microsoft.VisualStudio.Workload.ManagedDesktop --add Microsoft.VisualStudio.Workload.NetWeb --add Component.GitHub.VisualStudio --includeOptional --lang en-US`

这样，安装文件就下载到`C:\vslayout`

将`vslayout`文件夹拷贝到离线主机的C盘根目录



### 离线安装

然后运行下面的cmd命令：   

如果是`C++`开发环境：  

`c:\vslayout\vs_community.exe --noweb --add Microsoft.VisualStudio.Workload.NativeDesktop --includeRecommended`



如果是`.NET web  .NET desktop`开发环境：  

`c:\vslayout\vs_community.exe --noweb --add Microsoft.VisualStudio.Workload.ManagedDesktop --add Microsoft.VisualStudio.Workload.NetWeb --add Component.GitHub.VisualStudio --includeOptional`



更多命令参考：  

[Create an offline installation of Visual Studio](https://docs.microsoft.com/en-us/visualstudio/install/create-an-offline-installation-of-visual-studio?view=vs-2019#use-the-command-line-to-create-a-local-cache)





