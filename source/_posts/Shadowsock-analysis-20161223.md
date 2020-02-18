---
title: Shadowsock 分析与学习
date: 2016-12-23 08:12:49
tags: [Shadowsock, VPN, cryptography]
categories: [Tools]
---

Shadowsocks学习汇总资料，有引用，在此致谢前辈！
[ShadowSocks（影梭）不完全指南](http://www.auooo.com/2015/06/26/shadowsocks%EF%BC%88%E5%BD%B1%E6%A2%AD%EF%BC%89%E4%B8%8D%E5%AE%8C%E5%85%A8%E6%8C%87%E5%8D%97/)
GFW BLOG（功夫网与翻墙）:http://www.chinagfw.org


---

"距上次被警方约谈两天之后，任职于北京智者天下科技有限公司（知乎网）的 clowwindy 今日被要求彻底删除 [shadowsocks 项目](https://github.com/shadowsocks/shadowsocks-iOS/issues/124#issuecomment-133630294)于 Github 上发布的所有源码。目前所有子项目页面均显示“Removed according to regulations”。clowwindy 本人的推特账户也已被设为隐私模式，未经许可的关注者无法查看其过往言论。鉴于其所使用的 Apache License，已有部分用户自发创建了项目备份（例如[这里](https://github.com/Long-live-shadowsocks)和[这里](https://github.com/ShadowsocksBackup)）。令人毫无意外的是，V2EX上的相关讨论（[镜像](http://archive.is/offset=190/www.v2ex.com)）均被删除。"

**clowwindy推荐的提高水平的资料：**
1. HTTP: The Definitive Guide
2. TCP/IP Illustrated
3. Stanford 的 Cryptography http://online.stanford.edu/course/cryptography

网络监视器:https://nwmon.tifan.net/


wmon 是我最新的一个网络监视项目。使用 rrdtool 构建，这个监视器是轻量级的，可运行于几乎所有的平台上。

这个项目的目的是通过全球各主要数据中心对中国各重点网络交换中心的监视获取在极度拥塞前提下的连接中国以外与中国的网络的最佳链路以及时间—性能曲线并根据其对在海外或中国的对其相反的方向的连接性有使用需求的人群对网络路径的选择进行有针对性的优化。

---

### Introduction
ShadowSocks是由@clowwindy开发的一个开源Socks5代理。
A secure socks5 proxy, designed to protect your Internet traffic
A fast tunnel proxy that helps you bypass firewalls



---

### 关于Shadowsocks加密
rc4-md5是不安全的，不推荐使用。推荐使用AES-GCM或者chacha20-poly1305，其中如果是intel平台，AES-NI加速明显，而如果是考虑到客户端性能（ARM平台），chacha20-poly1305非常快。

RC4是流加密，AES是分组加密，RC4快于AES，RC4已经被攻破，极不安全。
如果是像Shadowsocks这样的应用场景，不需要高强度的加密，而只是作数据混淆，RC4是完全合适的。
rc4-md5已经被IETF废除并明令禁止使用了，新的浏览器已经移除支持了

table大概就是简单的密码表转置，性能好但已经可以归入古典密码学了。rc4是SS开发早期@clowwindy对密码学理解不充分的设计，没有正确使用IV，rc4-md5是正确的实现，两者性能应该接近。“rc4”会导致固定明文加密成固定密文，对抗选择明文攻击弱，统计特征明显。新加的OneTimeAuth主要是加个HMAC防御选择密文攻击。


---

相关测试数据：
`
encrypt and decrypt 20MB data
aes-128-cfb 0.368462085724s
aes-128-ofb 0.400309085846s
aes-192-cfb 0.452577829361s
aes-192-ofb 0.381041049957s
aes-256-cfb 0.418514966965s
aes-256-ofb 0.405379056931s
cast5-cfb 0.859935045242s
cast5-ofb 0.911785125732s
chacha20 0.429271936417s
rc4 0.154517173767s
rc4-md5 0.169504165649s
salsa20 0.44139790535s
`

原作者clowwindy曾经说过：
>    因为 chacha20 从 x86 上的性能来看，对速度的影响太小，提高还太有限，不如换个思路，因为通信包到了终端以后，走的都是电路，这里其实涉及到一个供电体系的问题，更换加密不如换一个电网，同一个 VPS，同一个路由器，但是，改用核电给路由器供电时，比火电丢包率会降低一个数量级，大大提高 TCP 吞吐率，经测试这是目前速度最快的供电方式，甚至优于水电，同理选 VPS 机房也要看供电，有些号称用了绿色能源，其实效果不好，这里面其实还涉及到选用 UPS 的型号，就不细说了
    另外说到硬件加速，连接路由器的网线也很重要，建议用六类屏蔽线，不过一定不能买那种超薄扁平的网线，会对带宽起到整形作用，突发上不去，看 4K 会受影响，数据可能不准，不过大概也体现了差异
    今天没有时间再次测试了，就发这么多吧。你们有一个好，出个新功能，写教程比别人都快，但试来试去的结论，太简单，有时太朴素了，我感觉你们还需要学习，提高自己的知识水平，将来如果写的教程有偏差，你们要负责

作者：曾阳
链接：https://www.zhihu.com/question/28252105/answer/53573849

---

你们都在想什么啊？SS的用途不是加密，而是混淆。但是仍然可以暴力破解，你设置数字密码一样是不行的。改进过的程序，IV不一样就分析不出差别来，可以抵御基于统计学的计算。

在通常应用中，SS主要是提供了一个不会被干扰的通道，你SS里面走的东西，一般都还有自己的TLS吧？ 如果VPN不会被干扰，谁会用这个东西。

作者：匿名用户
链接：https://www.zhihu.com/question/28252105/answer/53569320

---

IV的主要要求在于 uniqueness ，而非 secretness 。它的主要目标是在于防范 ECB 导致的统计攻击——同样的明文永远导致同样的密码，累计足够多的数据之后即可分析 pattern 或做统计来解密。这对于 block size 很小甚至是 byte-by-byte 的流式加密来说破坏更严重，最坏情况就是凯撒密码。

人们希望给明文—密文对应加入一些随机性，或者至少是不确定性。一个巧妙的办法就是把上一块的加密结果带到下一块里（可以是按位 xor 之类），这样同一块明文的密文就根据上下文不同而不同了。如此一来就需要有一个起始的密文，就是 Initialization Vector 。IV的加入为上述模式进一步增加了安全性（即使上下文一样，这个会话的IV不同密文也不同）。根据原理就可以知道，IV的唯一要求就是尽量不要重复，最好是难以预测的随机值。至于传输，完全可以是公开明文的。当然，一些协议可能把IV协商放在加密通道里进行，只是 defense by depth 罢了。

AES-256-*（甚至即使是没有IV的EBC）几乎在所有时候都比 RC4-MD5 安全（尤其是使用各种 OpenSSL 系的库或者 wrapper 时）。 RC4 本身的问题太多了， MD5 的冲撞都只是一个小方面。事实上，在有选择的时候，永远不要使用 RC4 ——在现代计算机中， RC4 的速度也不算不上是什么“优势”了：如果个人使用流量小，正常的 CPU（即使是虚拟机）都能应付且没有明显区别；如果商用流量大， AES 的硬件加速早就广泛普及了。

作者：马宏菩
链接：https://www.zhihu.com/question/28252105/answer/53562933

---

讨论shadowsocks不同加密方式的安全性没有意义。

shadowsocks是被设计来混淆数据，增加某Wall检查出流量特征所需的计算量，提高实时检测的成本，而不是加密。ss的作者多次强调过这一点([Correct username/password auth model · Issue #169 · shadowsocks/shadowsocks · GitHub]https://link.zhihu.com/?target=https%3A//github.com/shadowsocks/shadowsocks/issues/169)):
> "We don't need security. We need indistinguishability from random bytes."

说这些并不是指责ss的安全性，而是再三强调不要忘记ss作者的本意——这是一个帮你能用Google又或者是看别的什么奇怪网站的混淆工具，其意义是瞒过Wall的实时流量检测，而不是瞒过Wall后面的master minds。ss不是一个真正意义上的VPN，它无法保护你的数据安全。

作者：rlei
链接：https://www.zhihu.com/question/28252105/answer/53481328


---

https://www.futures.moe/feed.xml

https://busi.me/archives/33/

https://cn.v2ex.com/t/206358

defuse.ca/cbcmodeiv.htm
