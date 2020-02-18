---
title: Linux lubuntu 安装与使用
date: 2017-03-20 20:28:03
tags: [lunbuntu]
categories: [Linux]
---

lunbuntu 使用lxde界面，速度快，流畅，很少出现卡死情况。

当然安装速度也是飞一般的快

### 安装


### 使用
1. 设置root密码
`sudo passwd`

2. 安装chrome浏览器  
  在安装chrome是会出现  

  > dpkg: dependency problems prevent configuration of google-chrome-stable:  
   google-chrome-stable depends on libpango1.0-0 (>= 1.14.0); however:  
    Package libpango1.0-0 is not installed.  
   google-chrome-stable depends on fonts-liberation; however:  
    Package fonts-liberation is not installed.  

  需要解决依赖关系：  
  使用dpkg -i   soft.deb 的时候出现依赖没有安装  
  使用`apt-get -f -y install`  解决依赖问题后再执行dpkg安装deb包

3. 安装语言包  
  preference --> language support

4. lubuntu使用fcitx中文输入方法出现黑框框的处理  
以前用了几个版本都是一个问题，处理方法如下：  
安装`compton`  
`sudo apt-get install compton`  

在`~/`里面新建`.compton.conf`文件，在其中添加如下内容：  
```
shadow = true;            # Enabled client-side shadows on windows.
no-dock-shadow = true;        # Avoid drawing shadows on dock/panel windows.
no-dnd-shadow = true;        # Don't draw shadows on DND windows.
clear-shadow = true;        # Zero the part of the shadow's mask behind the

```

然后在命令行执行`compton &`和`killall fcitx-qimpanel`即可解决问题。  
如果想启动系统就自动修复的可以如下：  
打开 首选项->LXSession默认应用程序，在自动启动页的手动设为自动启动的程序处添加入`compton`，再到下面“已知的应用程序”里找到`Fcitx Qt IMPanel`，把勾去掉。  

在系统自启动加入该项目，在~/.config/lxsession/Lubuntu的autostart文件中。

5. 安装notify-send  
ubuntu的气泡提示是使用`notify-osd`来实现的，我们需要下载libnotify  
`apt-get install notify-osd`   (ubuntu 已经自带)  
`apt-get install libnotify-dev`

6. 安装7z
`apt-get install p7zip-full`
