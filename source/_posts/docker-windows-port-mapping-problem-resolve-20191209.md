---
title: Windows docker 端口映射问题解决
date: 2019-12-09 16:26:13
tags: [Docker]
categories: [Tools]
---





参考：[解决无法对docker容器进行端口映射的问题 ]( https://www.iteye.com/blog/baobeituping-2432389 )

在安装了一个Windows下安装了docker，并尝试在其中运行Nginx、owncloud等服务，但映射完毕之后，在主机的浏览器中，打开localhost:port无法访问对应的服务。

用百度尝试了下，始终找不到答案，难道这个问题大家都没遇到过么？于是使用Google，并用英文检索，一会就找到了对应的答案。



> The reason you’re having this, is because on *Linux*, the docker daemon (and your containers) run on the Linux machine itself, so “localhost” is also the host that the container is running on, and the ports are mapped to.
>
> On Windows (and OS X), the docker daemon, and your containers cannot run natively, so only the docker *client* is running on your Windows machine, but the *daemon* (and your containers) run in a VirtualBox Virtual Machine, that runs Linux.



原来，docker是运行在Linux上的，在Windows中运行docker，实际上还是在Windows下先安装了一个Linux环境，然后在这个系统中运行的docker。也就是说，服务中使用的localhost指的是这个Linux环境的地址，而不是我们的宿主环境Windows。我们可以通过cmd命令



```cmd
docker-machine ip default
```

找到这个Linux的ip地址，**一般情况下这个地址是192.168.99.100**，然后在Windows的浏览器中，输入这个地址，加上服务的端口即可启用了。


