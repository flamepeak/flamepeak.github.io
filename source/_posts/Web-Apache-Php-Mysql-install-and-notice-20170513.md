---
title: Apache Php Mysql install and notices
date: 2017-05-13 10:10:12
tags: [Apache, PHP]
categories: [Web后端]
---

### 参考
首先，感谢一下[Hcoder](http://www.hcoder.net/books/read/info/1135.html),这个网址的教程比较完善，可以参考

### Apache 安装
下载地址：[https://httpd.apache.org/](https://httpd.apache.org/)

Apache本身不提供已编译的安装包，只提供源码，如果你自己无法编译，可以选择下面这些官方推荐的第三方提供编译的网站。  
其中后两个是有名的wamp以及xampp集成环境，如果只想下载apache可以选择前三个网站。  
需要注意的是xampp只有32位的版本可用，在64位上的机器上有可能出问题，需要在64位机器上运行的话可以使用wamp


下载后会得到一个压缩文件（httpd-2.4.18-x64-vc11.zip），将文件解压并复制到安装目录。  
本教程安装目录为 d:\phpserver，apache的安装目录为 `d:\apache`  

**修改配置文件**

配置文件位置：`D:\apache\conf\httpd.conf`

修改配置文件的第38行  
`Define SRVROOT "/Apache24"`  
修改为  
`Define SRVROOT "D:\apache"`

**安装apche服务**

命令行下进入到apache下面的bin目录，输入  
`httpd -k install`  
把apache安装成windows后台服务。

**使用ApacheMonitor.exe来管理apache**

打开 `D:\apache\bin\ApacheMonitor.exe` 即可启动apacheMonitor来管理apache（可以发生到桌面快捷方式哦）。

点击启动apache 即可启动apache。

访问 localhost 可以观看效果。


### PHP7 安装与设置

下载地址：[http://windows.php.net/download/](http://windows.php.net/download/)

**php non-thread-safe和thread-safe的区别**

从PHP5.2.10版本开始，有 **None-Thread Safe** 与 **Thread Safe** 两种版本的可供选择，这两种版本有何不同，作为使用者来说又应该如何选择呢？  
先从字面意思上理解，None-Thread Safe就是非线程安全，在执行时不进行线程（thread）安全检查；  
Thread Safe就是线程安全，执行时会进行线程（thread）安全检查，以防止有新要求就启动新线程的 CGI 执行方式耗尽系统资源。


**PHP的两种执行方式：ISAPI和FastCGI**

**FastCGI** 执行方式是以单一线程来执行操作，所以不需要进行线程的安全检查，除去线程安全检查的防护反而可以提高执行效率，所以，如果是以 FastCGI（搭配IIS）执行 PHP ，都建议下载执行 non-thread safe 的 PHP （PHP 的二进位档有两种包装方式：msi 、zip ，请下载 zip 套件）。

而线程安全检查正是为ISAPI方式的PHP准备的，因为有许多php模块都不是线程安全的，所以需要使用Thread Safe的PHP(搭配apache)。


如果你还搞不清楚，那么只需知道你是php+apache还是php+iis组合  
non-thread-safe 非 线程安全与IIS搭配环境  
thread-safe 线程安全 与apache搭配环境  

另外php还有VC6 VC9版本区分  
VC6和VC9一个支持apache一个支持IIS  
VC9用在apache上也没问题,如果你用iis就必须用vc9的，如果你用apache vc6和vc9都可以用。  

**安装**

下载后得到php7的安装包（例:php-7.0.0-Win32-VC14-x64.zip）。  
解压后复制php文件到安装目录：`D:\php7`

**修改apache配置文件加载php 7**

apache配置文件位置：`D:\apache\conf\http.conf`

```
LoadModule php7_module "D:\phpserver\php\php7apache2_4.dll"
AddType application/x-httpd-php .php
AddType application/x-httpd-php .php3
```

**修改apache配置文件设置index.php为默认首页**
```
<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule>
```

安装完毕，新建一个index.php文件，内容如下：
```PHP
<?php
phpinfo();
？>
```
可显示出结果
