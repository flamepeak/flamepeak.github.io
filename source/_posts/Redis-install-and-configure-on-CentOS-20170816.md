---
title: Redis 在CentOS上的安装与配置
date: 2017-08-16 16:06:24
tags: [Linux, Redis]
categories: [Linux]
---


重要参考：[Install and Configure Redis on CentOS 7](https://www.linode.com/docs/databases/redis/install-and-configure-redis-on-centos-7)

[Redis 命令参考](http://redisdoc.com/index.html)

### Install Redis

1. Add the EPEL repository, and update YUM to confirm your change:

```bash
sudo yum install epel-release
sudo yum update
```

2. Install Redis:
`sudo yum install redis`

3. Start Redis:
`sudo systemctl start redis`

**Optional**: To automatically start Redis on boot:
`sudo systemctl enable redis`

在CentOS 6.5 中是没有 systemctl 命令的，可以通过chkconfig 和 service来实现。可以参考： [Linux中设置服务自启动的三种方式](http://www.cnblogs.com/shineqiujuan/p/4706028.html)

**具体如下：**
如果需要自启动某些服务，只需使用`chkconfig 服务名 on`即可，若想关闭，将on改为off

`--list`选项可查看指定服务的启动状态，chkconfig不带任何选项则查看所有服务状态
`chkconfig --list sshd`
`chkconfig --list`

如果想手动启动某服务，传统的方式是 `/etc/init.d 服务名 start`
还可以这样，`service 服务名 start`
对于redis： `service redis start`

***

### Verify the Installation

Verify that Redis is running with `redis-cli`:
`redis-cli ping`

If Redis is running, it will return:
`PONG`

***

### Configure Redis
[Install and Configure Redis on CentOS 7](https://www.linode.com/docs/databases/redis/install-and-configure-redis-on-centos-7)

上面这篇文章非常好，关于配置请前往阅读。


### Usage

查看所有key： `keys *`  
删除key: `del key_name`

区分大小写

