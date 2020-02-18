---
title: Scrapy 在Linux上使用问题与解决办法
date: 2017-08-14 11:24:34
tags: [Scrapy, MySQL, Python]
categories: [Python]
---


### How to disable cache in scrapy?
Cache can be disable in 2 ways:

+ Changing values in cache related settings in setting.py file. By Keeping `HTTPCACHE_ENABLED=False`
+ Or it can be done in runtime " scrapy crawl crawl-name --set HTTPCACHE_ENABLED=False

***

### mysql

#### 首先，安装mysql  
官网下载：  
https://dev.mysql.com/downloads/installer/
然后安装mysql  
#### 安装python mysql相关库  
`pip install mysql-python`  

  
Error 1:  
`error: Microsoft Visual C++ 9.0 is required (Unable to find vcvarsall.bat).`
解决办法：
安装Microsoft Visual C++ Compiler for Python 2.7
https://www.microsoft.com/en-us/download/details.aspx?id=44266

Error 2:  
`_mysql.c(34) : Fatal error C1083: Cannot open include file: 'config-win.h': No such file or directory`

解决办法：  
这个原因不知道是什么，网上有很多解决办法，也试了两个，最后解决了，说一下大致思路：
确认自己安装了 setuptools,   
然后下载安装mysql connector： http://dev.mysql.com/downloads/connector/c/6.0.html#downloads （有的人说要老版本的，我也不知道真假，都试试吧）

现在，使用`pip install mysql-python`看看还报不报错，如果还报错，那就把`MySQL Connector C 6.0.2`从`C:\Program Files\MySQL` 复制到 `C:\Program Files (x86)\MySQL`

再试试`pip install MySQL-python`看看还报不报错

这样我的问题就解决了。

检测安装是否成功`import MySQLdb`

For CentOS:
`yum install python-devel mysql-devel`

`pip install mysql-python`

On centos 7: `sudo yum install MySQL-python`

参考：http://www.cnblogs.com/rwxwsblog/p/4572367.html

***

 ### MySQL 使用
 `cd D:\Tools\MySQL5.5.25\bin`进入到mysql的bin目录下才可以输入 `mysql -h localhost -u root -p`

输入`show databases；`显示你有的数据库

***

### 安装python2.7.12

事先安装`yum install gcc`


```
wget --no-check-certificate wget https://www.python.org/ftp/python/2.7.12/Python-2.7.12.tgz
tar zxvf Python-2.7.12.tgz 
cd Python-2.7.12
./configure --prefix=/usr/local
make && make altinstall
```

可以修改`export PATH="/usr/local/bin:$PATH"`

安装Scrapy时，报错`Could not find a version that satisfies the requirement Twisted>=13.1.0 (from scrapy) (from versions: )
No matching distribution found for Twisted>=13.1.0 (from scrapy)`

这个错误可以通过：`yum install bzip2-devel`或`apt-get install libbz2-dev`来解决（For those who are using CentOS, Fedora or RHEL, the equivalent library that you need before compiling python is: `bzip2-devel`. The `libbz2-dev` only exists in Debian/Ubuntu.）


***

### 安装出现gcc错误

[error: command 'gcc' failed with exit status 1 on CentOS

](https://stackoverflow.com/questions/19955775/error-command-gcc-failed-with-exit-status-1-on-centos)

" error: command 'gcc' failed with exit status 1 ". the installation failed because of missing python-devel and some dependencies.

the best way to correct gcc problem:

You need to reinstall gcc , gcc-c++ and dependencies.

For python 2.7

```Python
$ sudo yum -y install gcc gcc-c++ kernel-devel
$ sudo yum -y install python-devel libxslt-devel libffi-devel openssl-devel
$ pip install "your python packet"
```

For python 3.4

```Python
$ sudo apt-get install python3-dev
$ pip install "your python packet"
```


***

### Scrapy爬虫 捕获403状态码抛出CloseSpider异常

[Scrapy爬虫 捕获403状态码抛出CloseSpider异常](http://blog.csdn.net/haipengdai/article/details/48526507)

1、爬数据的时候，有时会遇到被该网站封IP等情况，response的状态码为403，那么这时候我们希望能够抛出
CloseSpider的异常。

2、但是如scrapy官网提到的，Scrapy默认的设置是过滤掉有问题的HTTP response(即response状态码不在200-300之间)。因此403的情况会被ignore掉，意思就是我们不是处理这个url 请求的response，直接就忽略，也就是及时我们用response.status == 400判断没有作用，因为只有status处于200-300的请求才会被处理。

3. 如果我们想捕获或者处理403，或者其它如404或者500，这种请求时，我们在spider的类中把403放在handle_httpstatus_list中。如下就行。

```Python
 class MySpider(CrawlSpider):
    handle_httpstatus_list = [403]
 ```

或者将403放在HTTPERROR_ALLOWED_CODES设置中
即在settings中增加HTTPERROR_ALLOWED_CODES = [403]， HTTPERROR_ALLOWED_CODES默认是[]

http://doc.scrapy.org/en/1.0/topics/spider-middleware.html#httperror-allowed-codes

4. 设置完handle_httpstatus_list或者HTTPERROR_ALLOWED_CODES之后，就可以通过判断response.status == 403抛出CloseSpider异常，结束抓取。

***

### Scrapy 代理设置 proxy

[stackoverflow.com: Scrapy and proxies](https://stackoverflow.com/questions/4710483/scrapy-and-proxies)

[scrapy设置代理proxy](http://blog.csdn.net/haipengdai/article/details/50972983)

***

### python模块imghdr读图片后缀名

爬数据的时候常常遇到img标签的src中不包含图片的后缀名，如http://photos.prnewswire.com/prn/20100819/LA52539LOGO所示，

这时通过imghdr模块就能够把图片的后缀名读出来

```Python
import urllib2  
import imghdr  
  
url = 'http://photos.prnewswire.com/prn/20100819/LA52539LOGO'  
response = urllib2.urlopen(url)  
webpage = response.read()  
print imghdr.what('', webpage)  
```

***

### Scrapy Exceptions

1. DropItem，只能在item pipeline中使用

2. CloseSpider

```PYthon
def parse_page(self, response):
    if 'Bandwidth exceeded' in response.body:
        raise CloseSpider('bandwidth_exceeded')
```

3. IgnoreRequest

由Scheduler或者任何下载中间件发起

4. NotConfigured

5. NotSupported


### Scrapy HTTP Status Code 403

HTTP Status Code 403 definitely means Forbidden / Access Denied.  
HTTP Status Code 302 is for redirection of requests. No need to worry about them.  
Nothing seems to be wrong in your code.  

Yes, it's **definitely an anti-scraping measure implemented** by the site.  

在使用Tor之后，有些网站有图片验证，会弹出google验证码，就会出现这个错误



### Scrapy image download error 301

写代码的过程中遇到一个网站，网站正文可以下载下来，但是网站的图片无法下载， 错误如下：  

>  File (code: %(status)s): Error downloading file from http:/xxxxxx.xxx.xxx/xxx referred in

这个错误查了很多原因，甚至写了一个Filedownload，没有解决

在百般无奈之下，读了一下官方doc，找到了解决方案：

> Allowing redirections  
> By default media pipelines ignore redirects, i.e. an HTTP redirection to a media file URL request will mean the media download is considered failed.  
> To handle media redirections, set this setting to True:  

就是这样，在settings.py中加入：
`MEDIA_ALLOW_REDIRECTS = True`

以后有问题还是得先找官方文档，最权威啊
