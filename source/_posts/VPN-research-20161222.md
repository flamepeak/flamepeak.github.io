---
title: VPN 研究
date: 2016-12-22 14:26:19
tags: [VPN, cryptography]
categories: [Cryptography]
---

### VPN常用协议
两类：
+ 通用协议
  PPTP/L2TP/HTTP/socks5
+ 非通用协议
  OpenVPN/Tor/Goagent/Shadowsocks

1. IPsec协议
  目前是VPN中最流行的加密手段。IPsec是一种由IETF设计的端到端的确保基于IP通讯的数据安全的机制。IPsec支持对数据加密，同时确保数据的完整性。
2. PPTP协议
  PPTP（Point to Point Tunneling Protocol），即点对点隧道协议。该协议是在PPP协议的基础上开发的一种增强型安全协议，支持多协议虚拟专用网（VPN），可以通过密码验证协议（PAP）、可扩展认证协议（EAP）等方法增强安全性。可以使远程用户通过拨入ISP、通过直接连接Internet或其他网络安全地访问企业网。
  因为PPTP协议很好的和Windows操作系统兼容，所以PPTP协议最适合那些单独的计算机远程拨入公司本地网络所使用。
3. MPLS协议

4. SSL协议
  网络上常见的加密协议，例如通过SSL连接远程桌面，通过建立支持SSL的网站提高页面访问安全性等。同样，SSL也可以用于VPN。
5. IKEv2协议
  其在切换网络时，连接不会断开，加密强度最高。

---

主要的破网软件有GoAgent、花园网(GTunnel)与Tor系列、I2P、赛风、JAP、Freedur、红墙穿梭机（RWS）、Telex、超级大傻瓜代理器等。

主要的VPN代理服务器有proXPN、IPjetable、VPNPOP、Tenacy VPN、MacroVPN、VPN98、Loki Network Project 、FreeVPN、Hotspot Shield、 10JSQ等。

收费的推荐红杏和云梯
[大家翻墙都用什么工具](http://bar.freebuf.com/comment/5497)

---

### Shadowsocks
Shadowsocks帐号由4部分组成：
Hostname（Server IP）、Port、Password、加密模式（通常为AES-256-CFB）
