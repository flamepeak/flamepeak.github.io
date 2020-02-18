---
title: vmware NAT 的一些问题
date: 2019-04-23 11:32:41
tags: [vmware]
categories: [Network Security]
---

在使用Vmware做测试的时候，meterpreter生成的reverse_tcp，在NAT模式下无法回连，但是在bridge模式下可以回连。

在网络上大致找了一下原因：

[千万不要在VMWare的NAT模式下使用nmap](https://blog.csdn.net/dog250/article/details/52244906)

[为什么在VMWare的NAT模式下无法使用traceroute](https://blog.csdn.net/dog250/article/details/52194975)

[深入浅出VMware的组网模式](https://blog.csdn.net/dog250/article/details/7363534)

结论：  
1. NAT是一个代理，NAT模式可能会造成目的主机TCP连接统计的不准确
2. VMWare的NAT只支持比较常见的协议

**总之，不要在NAT模式下玩网络，玩不转的。**  
**总之，使用Bridge**


