---
title: 翻译：TP-link 设备调试协议(TDDP)研究
date: 2016-11-30 20:35:40
tags: IoT
---
本文首发于看雪论坛:[bbs.pediy.com](http://bbs.pediy.com/showthread.php?p=1455517#post1455517)
原文地址：[HERE](https://www.coresecurity.com/blog/story-about-tp-link-device-debug-protocol-tddp-research?utm_source=Twitter)    作者：Andres Lopez Luksenberg   

我之所以开始这项研究，是因为我试图简化WIFI渗透测试中用到的技术。我的想法是使用[WIWO](https://www.coresecurity.com/corelabs-research/open-source-tools/wiwo-wireless-workers)（这个工具是CoreSecurity去年发布的）在我主机的网络接口与路由器的WIFI接口之间建立一个透明通道。

像这样的研究的第一步是确定将要使用的组件。在我的这个项目里，我重点寻找合适的路由器。

经过一段时间的调查，我选择TP-Link [TL-WA5210g](http://www.tp-link.com/us/products/details/cat-5039_TL-WA5210G.html)，因为它满足安装定制固件的要求（允许我安装之前提到的工具），同时它是一个外部设备。
![TL-WA5210g](/sourcepictures/20161130/1.jpeg)

我拿到设备后注意到的第一件事是，硬件版本（2.0）与允许我定制或者hack的版本不同，我需要固件可以安装WIWO。我读了一些博客文章，发现有时硬件版本可以改变，但是固件版本存在于旧版中。不幸的是，这次不是这种情况，这个硬件版本设备的固件版本是我需要的，但是没有任何文档允许我对硬件版本（[TL-WA5210G_V2_140523](http://static.tp-link.com/resources/software/TL-WA5210G_V2_140523.zip)）进行降级。尝试通过Web应用程序降级，结果触发了一个一般性的错误信息。
![2](/sourcepictures/20161130/2.jpg)

---

### 连接到UART端口
鉴于初步结果，我决定尝试找到一个不同的方法来控制设备，使我可以安装想要的软件。我的第一种方法是焊接UART引脚，并尝试使用BusPirate（或者相似的东西）连接到设备，然后看看我能做些什么。（我在黑客学校学到的，拆开设备看到绿色电路板-so haxor bro！）
![UART_0_small](/sourcepictures/20161130/UART_0_small.jpg)

一旦连接到UART接口，我发现以下控制台有一组有限的命令可以运行。此菜单中显示的某些选项已禁用，如第一个和第二个：
![shell](/sourcepictures/20161130/shell.png)

我尝试了不同的数字和字母，想找到除菜单中显示的命令以外的其它命令。尽管我发现了一些隐藏的命令，但是没有找到令我感兴趣的命令。

接下来的选择是开始分析Web应用程序，我发现UART控制台中的大多数命令都可以从Web应用程序界面的一个隐藏菜单中触发 。这个菜单在OPenWRT维基百科中有描述。

因此，在我分析的这个设备中，隐藏菜单位于：
http://192.168.1.254/userRpm/NatDebugRpm26525557.htm


---

### 发现Bug
上一步找到的隐藏菜单包含了不同的按钮，这些按钮可以从设备中显示很多有趣的信息。不过，有一个奇怪的默认UDP端口被默认打开，当我第一次看到它时就引起了我的注意。
![port_listening](/sourcepictures/20161130/port_listening.png)

经过一些研究之后，我发现一个专利解释了这个与UDP端口相关的协议。
https://www.google.com/patents/CN102096654A?cl=en

TDDP是一个用于“调试”目的的简单协议。该协议使用单个UDP包发送请求或命令，通过在载荷中指定不同的消息类型。下图是TDDP数据数据包的格式：
![CN102096654BD00052](/sourcepictures/20161130/CN102096654BD00052.png)

我还发现了一些消息类型在那份专利文件中有描述。调试协议是进行这项研究时使用的很有趣的东西，用来啦uoqu关于设备状态的信息。我想知道到底哪些事情我可能实现。我已经从TP-Link的网站下载了这个设备运行的固件。

在下载完设备运行的固件后，我启动IDA并开始搜索协议处理程序，希望能够将专利文档中描述的内容与固件里实际执行情况进行匹配。我找到的这个专利描述了协议的版本2,但是在分析完固件之后，我发现它还引用了一个不同的版本（ver1）。
![selection_handler](/sourcepictures/20161130/selection_handler.png)
没有TDDP协议版本1的文档可用，所以我决定逆向工程处理函数，以了解其与版本2的主要区别。尽管数据包结构非常相似，但是我发现这两个版本的一个重要区别：版本1不支持在数据包的载荷中认证或加密，而版本2要求认证并且数据包被加密。

在分析v1消息处理函数后，我发现v2中的一些处理函数在v1中被使用。比如说***set_configuration***,***get_configuration***和***set_macaddr***，其中
+ ***set_configuration*** 处理函数用于设置设备配置
+ ***get_configuartion***处理函数用户获取设备配置
![handlers](/sourcepictures/20161130/handlers.png)

基于我目前所学到的东西，我准备开始写一个Python脚本实现TDDPv1协议。我首先关注的是***get_configuration***请求，试图收集返回的信息。

使用该脚本发送数据包之后，程序应答的数据看起来像一个键值对（***key-value***）配置文件。在阅读输出的配置时，我发现了以明文显示的凭据信息。（是的！在获取设备配置信息时是不需要认证的。）
![credentials](/sourcepictures/20161130/credentials.png)

这个漏洞的结果仅仅只是获取设备配置信息。尽管这很有趣，但是这离我的最初目标——安装定制固件——依然很远。再次阅读文档，我想一个用于调试目的的协议可能会有更多的问题，所以我开始静态逆向其余的消息处理函数。几个小时之后，我深入分析了set_configuration请求，并且发现了一个strcpy类型的漏洞，导致基于简单堆栈的缓冲区溢出。
![funcion_vulnerable](/sourcepictures/20161130/funcion_vulnerable.png)

在初步分析之后，我发现要利用这个漏洞的shellcode有一些限制，比如，它不能包含0或者空格，因为代码中有不同的检查。

有了这个漏洞，我可以控制TDDP服务的执行流程，指向我自己的代码，并且patch掉升级函数的验证机制，使我可以安装我想要的固件（包括旧版本）。

这个方法的主要问题是设备中没有可用的调试器。由于一些奇怪的原因，我之前焊接的UART引脚不能用了，我也不确定什么原因：-|）。现在，我可以从设备中获得的唯一的数据是进程的***PC***和***SP***寄存器的值，位于之前找到的隐藏网页中。

下图显示了进程列表。可以看到，***PC***和***SP***寄存器的详细情况。
![pc_y_sp_1](/sourcepictures/20161130/pc_y_sp_1.png)
我的策略是建立一个使用“跳转调试”（jump debugging）技术的漏洞利用方法，根据代码执行的成功或失败使用jmp指令跳转到不同的地址。

下图显示了使用这项技术操作***PC***寄存器的例子：
![pc_y_sp_2](/sourcepictures/20161130/pc_y_sp_2.png)

在这种情况下，文件描述符（值0xa）的值被载入寄存器，然后一个指向这个寄存器的jmp指令被执行，确保寄存器包含期望的值。


---

### 编写Exploit
几天后，使用上一节描述的技术，我可以使用一些ROP小工具控制***PC***寄存器;但是当我准备执行我自己的代码时（with there strictions explained previously）。。。代码没有执行。。。

我从来没有编写过MIPS架构的漏洞利用程序，在读了[这篇文章](http://www.devttys0.com/2012/10/exploiting-a-mips-stack-overflow/)之后，我明白了为什么它没有工作。原因是MIPS缓存没有被清理，并且写入的shellcode不可用。在刚才提到的博客描述的方法是通过调用***sleep()***函数清理缓存。至于我的设备，固件没有符号表，因此识别***sleep()***函数是件不容易的工作。为此，我开始学习MIPS缓存如何工作，以及如何清理缓存。

[这篇博客](http://cdn.imgtec.com/mips-training/mips-basic-training-course/slides/Caches.pdf)，使我搞清楚了MIPS缓存，它由两部分组成的。

一方面，我们有数据缓存（***D-cache***），另一方面，我们有指令缓存（***I-cache***）。清理这些缓存的过程是：将协处理器（coprocessors）的寄存器***TagLi***和***TagHi***设置为零，并调用指令“***cache 8, 0($a0)***”（用于清理***I-Cache***）和“***cache 9, 0($a0)***”（用于清理***D-Cahce***）。

检查固件，我发现一个函数的功能正是我需要的（可能是一个初始化程序）：
![clean_cache_1](/sourcepictures/20161130/clean_cache_1.png)
![clean_cache_2](/sourcepictures/20161130/clean_cache_2.png)


不过，我发现了这段代码的一个小问题
![clean_cache_3](/sourcepictures/20161130/clean_cache_3.png)


就像固件中其它函数的epilogs，这个函数结束时调用"***jr $ra***". "***$ra***"寄存器在“***jalr***”指令调用函数时设置。例如，如果你调用函数foo：***Jalr  foo***

“***$ra***”寄存器将被设置为返回地址。在我们的例子中，执行返回指令使用***jr***，而不是***jalr***，这是因为***jr***指令不设置“***$ra***”寄存器。

通常的解决办法是创建一个***ROP***链（对于MIPS架构称为***JOP***）：设置“***$ra***”为一个值（例如0x12345678），然后调用jump跳转到0x8016B910（缓存清理函数），这样做是为了先调用缓存清理函数，然后再执行0x12345678。

问题是“***$ra***”只在函数的epilogs中设置，就像这样：
![epilogo](/sourcepictures/20161130/epilogo.png)


上图显示了epilog。你可以看到，从堆栈获取数据之后，寄存器“***$ra***”被用来返回到调用函数。如果将寄存器“$ra”设置为0x8016B910（清理缓存函数），那么我将会对程序失去控制，因为创建了一个死循环。

但是，我们能做什么呢？

我意识到，在清理指令缓存***I-Cache***之后，新的指令被立即设置。这意味着我可以修改缓存清理函数(0x8016B910)的epilog，并替换指令***jr $ra***为***jr $fp***（或类似）;通过这个技巧我可以清理缓存并对程序保持控制，使其跳转到我的shellcode。

下图显示了函数指令修改前后的变化：
![before_after](/sourcepictures/20161130/before_after.png)

最后，shellcode代码应当仅对运行代码中固件检查进行patch，并调用无效的缓存指令应用那些变化使固件检查失效。

与[@_topo](https://twitter.com/_topo)交谈后，他建议我了解MIPS的区段布局，我发现如果使用***kseg1***而不是***kseg0***区段，那么MIPS缓存可以避免。([http://cdn.imgtec.com/mips-training/mips-basic-training-course/slides/Me...](http://cdn.imgtec.com/mips-training/mips-basic-training-course/slides/Memory_Map.pdf))但是，我不能测试。


---

### 后话
首先，好消息是：通过WAN端口是无法访问这个服务的。事实上，即使连接到WiFi也不能连接，所以需要通过使用有线进行连接。第二，需要指出的很重要的一点是，我们评估的固件版本发行于2014年。然而，同时，这是这个设备最新的可用版本。不幸的是，对与运行这个设备的人来说目前没有解决方案。

对于其它TP-Link设备，有新的固件版本。我们安装了从2015年起可用于TL-MR3020的最新固件版本，这个服务依然默认正在运行并处于监听状态。可以在固件中看到版本1的代码，尽管版本1中的处理程序较少（例如，用于获取和设置设备配置信息的处理函数已经被删除），因此这也是为什么我们现在不能说新设备是否受上述问题的影响。需要更多的研究来确定。对2016年以来的固件进行静态代码分析，因为我们没有设备可以测试，似乎反映了几乎相同的情况。

根据攻击情形和设备配置（强调一下，不暴露在WAN或WIFI下），这些漏洞最大的后果是允许攻击者用定制的固件（包括用于持久性控制的后门）替换原始固件，这将使攻击者可以从互联网访问内部网络。


---

### 结论
嵌入式设备的安全问题已经被人熟知，我们不准备花费大量时间检查所有常见的缺陷。但是让一个调试协议默认处于监听状态，而且不需要身份验证，是一个坏主意，供应商应该注意到这个问题。内存破坏漏洞同样很常见，希望能从前面研究描述中可以看出。操作系统供应商已经成熟了的安全开发实践应当被嵌入式设备供应商采用。例如，MIPS现在支持XI（执行禁止），类似于Intel中的NX位，以避免用户数据在内存中执行。但是，安全开发过程应当被采用，包括源代码审计和渗透测试。

