---
title: adb shell 使用
date: 2018-06-06 10:50:52
tags: [Android]
categories: [Android]
---

1. 首先，下载安装Android Studio  
2. 运行android studio会提示下载安装SDK，自动下载安装即可  
3. 在android Studio界面，找到Configure->Android SDK打开可以看到SDK的路径，也可以修改路径  

### 遇到的问题  
一. Windows下adb命令找不到，应该是环境变量的问题  

1. 找到SDK安装路径，adb.exe位于platform-tools文件夹下， 复制路径`D:\Dev\Android\SDK\platform-tools`    
2. 在系统环境变量修改界面，新建系统环境变量 Android， 值设为上一步的路径  
3. 修改环境变量Path值, 添加%Android%  

二. adb shell 连接夜神模拟器失败解决方案  
正常安装后运行`adb devices`看不到夜神模拟器，请参考以下方法：  

1. 进入设置-关于平板电脑里面 ，点击版本号将夜神模拟器点击成开发者模式,点击5下就可以

2. 找到android 的adb.exe 去替换夜神模拟器 安装目录bin下面的nox_adb.exe

3. 配置nox_adb.exe 的安装目录到 系统环境里面，即新建系统环境变量nox_adb，值设为nox_adb.exe的路径

4. 重新启动androidStudio 和 夜神模拟器




