---
title: shadowsocks server 配置
date: 2016-12-28 20:57:36
tags: [VPN, Shadowsocks]
categories: [Tools]
---


梯子商店： [Shadowsocks指导篇（总结归类）](https://doub.io/ss-jc26/)

[逗比根据地](https://doub.io/)

## Shadowsocks
### Install
#### Debian/Ubuntu
```
apt-get install python-pip
pip install shadowsocks
```

#### CentOS
```
yum install python-setuptools $$easy_install pip
pip install shadowsocks
```

---

### Usage
```
ssserver -p 443 -k password -m rc4-md5
```
如果要后台运行：
```
sudo ssserver -p -k password -m rc4-md5 --user nobody -d start
```
如果要停止：
```
sudo ssserver -d stop
```

日志文件：
```
sudo less /var/log/shadowsocks.log
```

或者,使用配置文件：
配置文件：`/etc/shadowsocks.json`
```
{
    "server":"172.31.17.22", //对于没有独立ip使用NAT上网的模式的，要查看自己ip
     "server_port":878, //自定义
     "local_port":108,
     "password":"×××××××",
     "timeout":600,
     "method":"aes-256-cfb"
}

```
命令：
```
ssserver -c /etc/shadowsocks.json -d start

```


## ShadowsocksR

Client: https://github.com/shadowsocksrr/shadowsocksr-csharp/releases

参考网址： [ShadowsocksR 单用户版服务端安装教程](https://doub.io/ss-jc11/)

Server config:

```
{
    "server": "172.31.22.19",
    "server_ipv6": "::",
    "server_port": 8777,
    "local_address": "127.0.0.1",
    "local_port": 1080,

    "password": "cddddddddddddbie",
    "method": "aes-128-ctr",
    "protocol": "auth_aes128_md5",
    "protocol_param": "",
    "obfs": "tls1.2_ticket_auth_compatible",
    "obfs_param": "",
    "speed_limit_per_con": 0,
    "speed_limit_per_user": 0,

    "additional_ports" : {}, // only works under multi-user mode
    "additional_ports_only" : false, // only works under multi-user mode
    "timeout": 120,
    "udp_timeout": 60,
    "dns_ipv6": false,
    "connect_verbose_info": 0,
    "redirect": "",
    "fast_open": false
}
```

注意客户端进行设置的时候参数要和服务器一致
