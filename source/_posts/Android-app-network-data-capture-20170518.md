---
title: Android app 网络数据抓取相关设置
date: 2017-05-23 15:39:55
tags: [Network Security, Fiddler, BurpSuite]
categories: [Android]
---


## 利用Fiddler对Android模拟器网络请求进行抓包

[利用Fiddler对Android模拟器网络请求进行抓包](http://www.jianshu.com/p/7135afa4a828)

1.打开模拟器设置（Settings），点击Wi-Fi。

2.长按WiredSSID，选择Modify network（修改网络）

3.勾上Show advanced options，输入代理服务器地址和端口（关键）。

注意：代理地址和端口的设置是关键，Genymotion模拟器中ip为10.0.3.2，Android模拟器中ip为10.0.2.2，端口为FIddler监听的端口，默认为8888，最后保存确认。


## 如何监控Android模拟器的HTTP访问情况

重要参考文章：
[如何监控Android模拟器的HTTP访问情况](http://blog.csdn.net/asmcvc/article/details/10448793)

问题场景：在调试某个应用时，需要监控应用与服务器之间的HTTP通讯。

解决思路：第一时间想到Fiddler2。Android官方文档提到用TCPDump获得通讯封包或实时监控，好是好，有点高射炮打蚊子的意思。 Fiddler2这个工具，界面友好，功能足够使用，可以说是Windows上最好的HTTP监控工具。Fiddler2用起来很方便，运行之后，即在本 机建立一个代理（缺省设置为127.0.0.1:8888），其他访问HTTP的应用程序，把代理设为同一地址和端口即可。

实施过程：

按照文档的指导，在启动模拟器时，使用`-http-proxy`参数，设置127.0.0.1:8888为模拟器的上网代理，结果失败。Fiddler2没有抓到通讯应答。

上网搜索，许多文章或帖子（包括英文的和中文的）都说要修改模拟器的系统设置数据库，添加一条HTTP_PROXY的记录。尝试后失败。

解决方案：

再细读文档中有关模拟器的部分，看到Network Address Space一段，忽有所悟。这段文字是这么写的——

> Each instance of the emulator runs behind a virtual router/firewall service that isolates it from your development machine's network interfaces and settings and from the internet. An emulated device can not see your development machine or other emulator instances on the network. Instead, it sees only that it is connected through Ethernet to a router/firewall.

（译文）模拟器在虚拟路由器/防火墙服务后面运行，这套路由器/防火墙服务隔离于开发机的网络界面、设置，也与互联网相隔离。模拟设备不能访问开发机或网络上的其他模拟器。它看到的只是自己通过以太网连接到一个路由器/防火墙。

这下清楚了，模拟器压根不知道127.0.0.1是个啥地址，也压根访问不到。在127.0.0.1上设置的代理，自然也抓不到任何通讯应答了。文 档接着写道，这个虚拟防火墙/路由器管理从10.0.2.2至10.0.2.24，模拟器的IP地址是10.0.2.15，而模拟器看到的开发机地址则是 10.0.2.2。也就是说，从模拟器的立场看，Fiddler2是在10.0.2.2上运行的。

通过上面的文章，收获很大，在模拟器中进行了设置：  
首先， 模拟器依次打开：设置-无线和网络-移动网络-接入点名称（APN）， 如果已经有了一个APN，可以选择新建一个，名称随意写，APN可写为internet。

其次， 代理设置为：10.0.2.2， 端口设置：8888， 除了MCC，MNC不变， 其它都修改为未设置。

然后， 保存（必须要保存）， 然后回到上一步选择启用新建的这个APN。

最后，需要在Fiddler所在的主机上，添加一个ip地址： 10.0.2.2.

这样应该就可以了。



## BurpSuite的Proxy设置

至于BurpSuite的Proxy设置：  
在Proxy->Setting里Bind to port 设为8888, Bind to address设为Loopback only， 同时选择Support Invisible proxying。认证可以选择Use a self-signed certificate。
然后，在IE里设置代理，在LAN setting中，Proxy Server 设置Address为10.0.2.2，Port为8888(如果不行，设置127.0.0.1：8888试试).

这样应该就可以了。

注意，如果还是不行的话，要关闭杀毒软件，例如Avast。



## 浅谈HTTPS以及Fiddler抓取HTTPS协议
重要参考：[浅谈HTTPS以及Fiddler抓取HTTPS协议](http://www.jianshu.com/p/54dd21c50f21)

在抓取android模拟器http数据时，上面的方法没有问题，但是在处理HTTPS数据时，Fiddler有时候会抓取不到，至于有没有漏掉使用wireshark可以核实。

至于HTTPS原理以及Fiddler抓取HTTPS原理，可以参考链接上面的文章
下面对Fiddler设置进行摘要：

（一）首先对Fiddler进行设置：打开工具栏->Tools->Fiddler Options->HTTPS
选中Capture HTTPS CONNECTs，因为我们要用Fiddler获取手机客户端发出的HTTPS请求，所以中间的下拉菜单中选中from remote clients only。选中下方Ignore server certificate errors.（注意： 在我个人的设置中与参考文章的不一样，下来菜单选择from all processes依然可以，请各位自己尝试）

![fiddler_https1.png](/sourcepictures/2017/05/18/fiddler_https1.png)

（二）然后，就是手机安装Fiddler证书。  
这一步，也就是我们上面分析的抓取HTTPS请求的关键。  
操作步骤很简单，打开手机浏览器，在浏览器地址输入代理服务器IP和端口，会看到一个Fiddler提供的页面。  

![fiddler_https2.png](/sourcepictures/2017/05/18/fiddler_https2.png)

接着点击最下方的FiddlerRoot certificate，这时候点击确定安装就可以下载Fiddler的证书了。

下载安装完成好后，我们用手机客户端或者浏览器发出HTTPS请求，Fiddler就可以截获到了，就跟截获普通的HTTP请求一样。
