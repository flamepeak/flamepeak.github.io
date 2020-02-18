---
title: Tunnel among shadowsocks servers
date: 2018-05-28 17:23:15
tags: [Python, Shadowsocks, VPN]
categories: [Tools]
---

### 目的
主要目的是实现Shadowsocks的中转（中继），或者说转发。

### 致谢
本文的很多内容参考自：[利用python-proxy翻墙](http://briteming.blogspot.com/2018/03/python-proxy.html)  
在此表示感谢（FISHMAN：人活世上,为了利益和开心而已）

### 主要内容
主要实现的手段是 [python-proxy](https://github.com/qwj/python-proxy)，HTTP/Socks/Shadowsocks/Redirect asynchronous tunnel proxy implemented in Python3 asyncio.

#### Python3与pip3的安装  
由于pproxy需要使用asyncio，所以要安装python3.6以上版本

可能需要的依赖包：
```Bash
yum -y groupinstall "Development tools"
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
```

获取python3

```Bash
wget https://www.python.org/ftp/python/3.6.4/Python-3.6.4.tgz
```

解压、编译python3.6.4环境：

```Bash
tar zxvf Python-3.6.4.tgz
cd Python-3.6.4
./configure --prefix=/usr/local/python-3.6.4
make 
make install
```

最后创建软链接:

```Bash
ln -s /usr/local/python-3.6.4/bin/python3 /usr/bin/python3
ln -s /usr/local/python-3.6.4/bin/pip3 /usr/bin/pip3
```

#### 安装pproxy

`pip3.6 install pproxy`

如果不行的话，可以用下面方法解决：  
使用git下载到本地，  
`git clone https://github.com/qwj/python-proxy.git`  

然后,  
```Bash
cd python-proxy
sudo python3 setup.py install
```

添加环境变量PATH（对所有系统用户生效，永久生效）  
修改 `/etc/profile` 文件，在文件末尾加上如下两行代码   
```Bash
PATH=$PATH:/usr/local/python-3.6.4/bin 
export PATH
```

最后执行命令 `source /etc/profile` 或执行点命令 `./profile` 使其修改生效。

这样运行pprxoy应该就可以了

#### 使用二三事

安装完成后，得到的可执行文件是pproxy.  
```Bash
pproxy -i ss://aes-256-cfb:my-password@:12345 > /dev/null &
```

最好用daemonize来运行：
```Bash
daemonize -c . /usr/local/python-3.6.4/bin/pproxy -i ss://aes-256-cfb:my-password@:12345
```

这样在服务器上，就成功搭建了一个ss服务器-服务器的ip就是你vps的ip,端口号为12345，密码为my-password,加密方式为aes-256-cfb。把这4个参数填入你在本地机器的ss客户端里，即可用
该ss客户端翻墙。

在你的本地机器（linux桌面系统或mac), 按照上面的蓝色文字，编译python3.6.4环境。  
然后运行:   

```Bash
pproxy -i ss://:8888 -r ss://aes-256-cfb:my-password@my-vps-ip:12345
```

然后设置本地浏览器的socks5代理服务器为127.0.0.1:8888 , 本地浏览器即可翻墙。

**中转代理**
local--> A.A.A.A --> ss server(B.B.B.B)

local:  本地可以直接使用界面client,   
关键配置：ip: A.A.A.A
端口：8888

A.A.A.A : 
```Bash
pproxy -i ss://aes-256-cfb:password@:8888 -r ss://aes-256-cfb:password@B.B.B.B:8888 -v
```

SS Server（B.B.B.B）:
```Bash
pproxy -i ss://aes-256-cfb:password@:8888 -v
```

大概就是这个模式
