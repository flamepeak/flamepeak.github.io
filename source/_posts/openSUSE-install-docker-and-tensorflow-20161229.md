---
title: openSUSE 安装Docker和 Tensorflow
date: 2016-12-29 16:13:35
tags: [openSUSE, Docker, Tensorflow]
categories: [Linux]
---

### 安装Docker
1. 安装Docker包
```
su
zypper in docker
```
2. 开启Docker daemon
```
systemctl start docker
```
3. 测试Docker安装情况
```
docker run hello-world
```

### 配置Docker启动选项
需要开机启动`docker daemon`，使用下面命令：
```
systemctl enable docker
```
在安装docker时，自动创建了一个docker用户组。除了root用户之外，其它用户必须是docker用户组的一员才能与Docker daemon交互。使用下列命令将用户加到docker用户组：
```
/usr/sbin/usermod -a -G docker <username>
```
如果出现下列错误：**Cannot connect to the Docker daemon. Is 'docker daemon' running on this host?**
那就检查`DOCKER_HOST`环境变量是否设置：
```
env | grep DOCKER_HOST
```
如果设置了，上面的命令会输出一个值。
```
unset DOCKER_HOST
```

### 启用外部网络链接
如果需要启用外部网络访问权限，你必须启用`net.ipv4.ip_forward`规则。
```
sudo vim /etc/sysconfig/SuSEfirewall2
```
设置`FW_ROUTE="yes"`


### 卸载Docker
```
sudo zypper rm docker
rm -rf /var/lib/docker
```

### `我的辛酸路。。。`
现在（2016.12），Docker官方网站的windows版本要求win10,没有就去下载吧。
BIOS中要开启Hyper-V,有些机器是VT-x；前期Docker报错基本都是这个原因。
另外，如果win10在虚拟机里的话，例如VMware，要在虚拟机的设置里启用Hyper-V和VT-x；

重要的一点，假如：你的Docker启动过程中出现”内存不足“，服务无法启动的话。请在任务栏右键Docker图标，选择设置->Advanced，将内存的数字改小，然后Apply，这样就会服务会重新启动。

在win10里，我是用管理员权限的cmd运行的。

走过的坑，希望后人借鉴。




---

### 安装Tensorflow
#### 标准按照
4 Docker images:
+ `gcr.io/tensorflow/tensorflow`: TensorFlow CPU binary image.
+ `gcr.io/tensorflow/tensorflow:latest-devel`: CPU Binary image plus source code.
+ `gcr.io/tensorflow/tensorflow:latest-gpu`: TensorFlow GPU binary image.
+ `gcr.io/tensorflow/tensorflow:latest-devel-gpu`: GPU Binary image plus source code.

启动一个Docker容器：
```
docker run -it -p 8888:8888 gcr.io/tensorflow/tensorflow
```

#### 离线环境安装
针对不能上网的机器，或者在墙内无法连接gcr.io的朋友
方法来自stackoverflow:
1. 在可以上网的机器上运行：
`docker pull gcr.io/tensorflow/tensorflow:latest`
2. 保存镜像到文件
`docker save -o tensorflow_image.docker gcr.io/tensorflow/tensorflow:latest`
3. 将文件拷贝到无法上网的机器，然后运行：
`docker load < tensorflow_image.docker`

**注意：**此方法在pull时必须登录Google账户，怎么登？去浏览器里登录了，访问gcr.io/tensorflow/tensorflow
在win10里，我是用管理员权限的cmd运行的。


---

### 运行
#### 基于jupyter
安装之后，启动带有TensorFlow image的Docker容器：
```
docker run -it -p 8888:8888 gcr.io/tensorflow/tensorflow
```

The option `-p 8888:8888` is used to publish the Docker container᾿s internal port to the host machine, in this case to ensure Jupyter notebook connection.

The format of the port mapping is `hostPort:containerPort`. You can specify any valid port number for the host port but have to use `8888` for the container port portion.

#### 挂载本地目录与文件
当我们在docker容器中运行服务时,经常会有需要将服务数据持久化的场景。那具体保存到哪里呢?容器的无状态特性决定了我们不应该将数据保存在容器中, 因为容器一旦重启, 文件数据就会丢失.一般应该使用volume参数,通过挂载外部文件系统到Docker容器中来保存数据.

docker可以支持把一个宿主机上的目录挂载到镜像里。
```
docker run -it -v /home/sun/Downloads:/mnt ubuntu64 /bin/bash
```
通过`-v`参数，冒号前为宿主机目录，必须为绝对路径，冒号后为镜像内挂载的路径。
或者
```
docker run -it -v /home/sun/Downloads:/mnt -p 8888:8888 ubuntu64
```
这样就可以在docker中处理文件了。
默认挂载的权限为读写。如果指定为只读，可以用：`ro`
```
docker run -it -v /home/sun/Downloads:/mnt:ro  ubuntu64 /bin/bash
```

另外，docker还提供了一种高级的用法叫数据卷。数据卷其实就是一个正常的容器，专门用来提供数据卷供其它容器挂载的。此处不详述。

然后更改jupyter notebook的工作目录：
```
jupyter notebook --generate-config
```
如果显示文件已经存在的话，就选不覆盖。然后，打开提示的文件路径，修改配置文件：
```
vi /root/.jupyter/jupyter_notebook_config.py
```
添加：
```
c.NotebookApp.notebook_dir = '/mnt'
```
实在不行的话，就使用笨方法：将需要的文件夹复制到`/notebooks`下面，
```
cp -r /mnt/tensorflow-r0.12/ /notebooks
```



### 检测安装情况
``` python
>>> import tensorflow as tf
>>> hello = tf.constant('Hello, TensorFlow!')
>>> sess = tf.Session()
>>> print(sess.run(hello))
Hello, TensorFlow!
>>> a = tf.constant(10)
>>> b = tf.constant(32)
>>> print(sess.run(a + b))
42
>>>
```
