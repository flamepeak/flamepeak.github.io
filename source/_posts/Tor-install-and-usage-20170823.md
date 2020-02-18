---
title: Tor polipo在CentoS上的安装与使用
date: 2017-08-23 10:24:12
tags: [Tor, CentOS]
categories: [Tools]
---

推荐阅读： [Setting up Tor Proxy and Hidden Services in Linux](http://www.devdungeon.com/content/setting-tor-proxy-and-hidden-services-linux)


### Install

Tor no longer recommends using their repo for CentoOS and to instead use epel repos. You'll just end up with a very old version out of their repos.

Instead do:

```Bash
yum install epel-release
yum install tor
```

### Configure Tor

```Bash
# Review/modify the configuration
vim /etc/tor/torrc
```
You can change the default port, or specify multiple IP addresses and ports for binding.

```Bash
SOCKSPort 9050 # Default
SOCKSPort 10.10.1.23:9999 # Bind to specific IP/port
```
只更改第一个也可以，开了第二个有可能出错


### Start Tor

```Bash
# Control the service using standard systemctl or service commands
service tor restart
systemctl restart tor
```

### Test Tor

Test out your connection by setting your browser to use SOCKS5 localhost:9050 or follow the examples using cURL or Go.
Making Tor requests with command-line cURL
Making HTTP Tor requests with Go

#### Making Tor Requests with command-line cURL

Making requests with the Tor network is easy with cURL. Since Tor uses the common SOCKS5 protocol, it works well with anything that has SOCKS5 support. cURL comes with a simple command-line option to specify a proxy.

```Bash
# Linux default Tor proxy port is 9050
curl --socks5-hostname localhost:9050 https://check.torproject.org
```

```Bash
# Windows/Tor Browser Bundle default to port 9150
curl --socks5-hostname localhost:9150 https://check.torproject.org
```
通过这种方法会检测出是否经过Tor访问，在Server版本中由于没有界面会显示html文本，可以看到：Congratulations. This browser is configured to use Tor.Your IP address appears to be: 198.X.X.X

#### Making Tor HTTP Requests with Go

Go provides standard packages for working with HTTP, URLs, and proxies. Below is an example of how to make an HTTP request using a Tor proxy with the Go programming language. The key difference from making a standard HTTP request is that you must configure the HTTP client to use the proxy. This is done by creating a proxy dialer, creating an HTTP transport that uses that proxy dialer, and then creating an HTTP client that uses that transport. Check out the example below.

```Go
package main

import (
	"io/ioutil"
	"log"
	"net/http"
	"net/url"
	"time"

	"golang.org/x/net/proxy"
)

// URL to fetch
var webUrl string = "https://check.torproject.org"

// Specify Tor proxy ip and port
var torProxy string = "socks5://127.0.0.1:9050" // 9150 w/ Tor Browser

func main() {
	// Parse Tor proxy URL string to a URL type
	torProxyUrl, err := url.Parse(torProxy)
	if err != nil {
		log.Fatal("Error parsing Tor proxy URL:", torProxy, ".", err)
	}

	// Create proxy dialer using Tor SOCKS proxy
	torDialer, err := proxy.FromURL(torProxyUrl, proxy.Direct)
	if err != nil {
		log.Fatal("Error setting Tor proxy.", err)
	}

	// Set up a custom HTTP transport to use the proxy and create the client
	torTransport := &http.Transport{Dial: torDialer.Dial}
	client := &http.Client{Transport: torTransport, Timeout: time.Second * 5}

	// Make request
	resp, err := client.Get(webUrl)
	if err != nil {
		log.Fatal("Error making GET request.", err)
	}
	defer resp.Body.Close()

	// Read response
	body, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		log.Fatal("Error reading body of response.", err)
	}
	log.Println(string(body))
	log.Println("Return status code:", resp.StatusCode)
}
```


***

***

### Polipo

```Bash
yum install  texinfo  -y       #编译时依赖软件

git clone https://github.com/jech/polipo.git
cd polipo
make all
make install
```

建立配置文件

官方文档中，默认的配置文件路径为： `~/.polipo or /etc/polipo/config`

```Bash
mkdir /etc/polipo
vim /etc/polipo/config
cp config.sample /opt/polipo/config
```
编辑配置文件

在使用TOR时，如下配置以下内容：

Polipo is configured by setting a number of configuration variables in Polipo's configuration file, which is either ~/.polipo or /etc/polipo/config, whichever exists. In order to tell Polipo to use tor and not to cache any fetched pages on disk, you will want to add the following lines to your configuration file:

> socksParentProxy = localhost:9050   #注意此处没有加引号  
  diskCacheRoot=""

You will also want to disable the local configuration interface in order to protect yourself from browser vulnerabilities:

> disableLocalInterface = true

如果想后台运行Polipo，还需要：

> daemonise = true

注意： polipo配置文件的路径要注意，如果没有把握的话就在运行polipo的时候加上 `-c`参数，例如：`polipo -c /opt/polipo/config`,这是我走过的坑


**以下设置对于CentOS 7 可行**

新建启动脚本

```Bash
vim /etc/systemd/system/polipo.service
```


内容为

```Bash
[Unit]
Description=polipo web proxy
After=network.target

[Service]
Type=simple
WorkingDirectory=/tmp
User=polipo
Group=polipo
ExecStart=/usr/bin/polipo -c /opt/polipo/config
Restart=always
SyslogIdentifier=Polipo

[Install]
WantedBy=multi-user.target
```


打开防火墙

```Bash
firewall-cmd --permanent --add-port=8123/tcp
firewall-cmd --reload
```

尝试启动服务

```Bash
systemctl start polipo
```

测试现在的ip地址：

`curl --proxy localhost:8123 ifconfig.me`
