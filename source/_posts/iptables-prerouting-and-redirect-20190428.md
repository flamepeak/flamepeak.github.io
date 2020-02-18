---
title: iptables prerouting and redirect 20190428
date: 2019-04-28 16:56:54
tags:
categories: [Network Security]
---

转自：[当服务器只开web服务并且防火墙不准服务器对外主动发起链接时，这样来突破](https://github.com/tom0li/security_circle/blob/master/15288418585142.md)


当服务器只开web服务并且防火墙不准服务器对外主动发起链接时，这样来突破！

当我在渗透测试的时候，拿到了web shell可以执行命令时，总想反弹shell出来方便进一步渗透，但是我碰到的环境安全性都做的很好，防火墙策略直接禁止服务器对外发起链接。这样就没法反弹了。

然后我自己有想过突破的方法，我当时想到的是重新封装http的数据包，到服务器后再拆包，转到22端口上去，这样做很复杂，一直没去实现。

今天在freebuf看到这篇文章，就忍不住转过来分享一下，作者在服务器上直接对源端口进行检查，然后将特定的源端口数据通过iptables nat表中PREROUTING 链配合 REDIRECT 重定向到本机22端口去，当时怎么没想到。这个在一些防火墙策略做的很完善的环境下，很有用！ [远程遥控IPTables进行端口复用](https://threathunter.org/topic/594545184ea5b2f5516e2033)


[远程遥控IPTables进行端口复用](https://threathunter.org/topic/594545184ea5b2f5516e2033)


