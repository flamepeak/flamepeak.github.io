---
title: v2ray 一些汇总
date: 2019-04-18 17:57:45
tags: [Tools, V2Ray]
categories: [Tools]
---

参考了一些网址：[V2RAY 配置：WebSocket + TLS + CDN](https://koppa.cf/2018/07/041653/)

[使用Cloudflare中转V2Ray流量](https://github.com/233boy/v2ray/wiki/%E4%BD%BF%E7%94%A8Cloudflare%E4%B8%AD%E8%BD%ACV2Ray%E6%B5%81%E9%87%8F)

### 介绍

本文介绍的方法适合VPS的IP已经被墙的情况下，继续用梯子。

方式：V2Ray+WebSocket+TLS+CDN

1. V2Ray是一个集成了各种翻墙协议的软件，包括Socks（目前接触到的是本机到本机）、HTTP、Shadowsocks（目前接触到的是本机到远程）、VMess等，传输载体可以是TCP、mKCP、WebSocket等

2. 使用WebSocket：因为CDN可以转发WS流量

3. 使用https（TLS）伪装网站，请求流量数据

4. 使用CDN转发流量，同时作为连接VPS的跳板，使VPS复活和隐藏





### 域名
要使用 CDN 来加速网站，必须要有自己的域名。

域名在 https://www.freenom.com/ 申请，1年免费，期满后可以无限免费续期。

域名后缀为 .TK / .ML / .GA / .CF / .GQ ，很符合博主个人使用的需求。

注意这个网站有时候不好使，把adblock之类插件关了

### cloudflare

在 Cloudflare 的 Overview 选项卡可以查看域名状态，请确保为激活状态，即是： Status: Active

在 DNS 选项卡那边添加一个 A 记录的域名解析，假设你的域名是 233blog.com，并且想要使用 www.233blog.com 作为翻墙的域名
那么在 DNS 那里配置，Name 写 www，IPv4 address 写你的 VPS IP，务必把云朵点灰，然后选择 Add Record 来添加解析记录即可
(如果你已经添加域名解析，请务必把云朵点灰，即是 DNS only)


### V2Ray Install

使用懒人脚本：
```bash
sudo bash <(curl -L -s https://install.direct/go.sh)
```

传输协议选择 WebSocket + TLS (即是选择 4 )，V2Ray 端口随便，不要是 80 和 443 即可，然后输入你的域名，域名解析 Y ，自动配置 TLS 也是 Y ，其他就默认吧，一路回车。等待安装完成
如果你的域名没有正确解析，安装会失败，解析相关看上面的 添加域名解析

安装完成后会展示 V2Ray 的配置信息，并且会询问是否生成二维码等，不用管它，直接回车

然后输入 v2ray status 查看一下运行状态，请确保 V2Ray 和 Caddy 都在运行


### 设置 Crypto 和 开启中转
确保 Cloudflare 的 Crypto 选项卡的 SSL 为 Full

并且请确保 SSL 选项卡有显示 Universal SSL Status Active Certificate 这样的字眼，如果你的 SSL 选项卡没有显示这个，不要急，只是在申请证书，24 小时内可以搞定。

然后在 DNS 选项卡那里，把刚才点灰的那个云朵图标，点亮它，一定要点亮一定要点亮一定要点亮

云朵图标务必为橙色状态，即是 DNS and HTTP proxy(CDN)





