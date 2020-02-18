---
title: Anaconda 使用与问题解决
date: 2017-02-15 19:50:36
tags: [Python, Anaconda]
categories: [Python]
---

### Conda 环境管理
Conda的环境管理功能允许我们同时安装若干不同版本的Python，并能够自由切换。
```Bash
# 创建一个名为python34的环境，指定Python版本是3.4（不用管是3.4.x，conda会为我们自动寻找3.4.x中的最新版本），会提示环境的路径：Package plan for installation in environment /root/anaconda2/envs/python34
conda create --name python34 python=3.4

# 安装好后，使用activate激活某个环境
activate python34 # for Windows
source activate python34 # for Linux & Mac
# 激活后，会发现terminal输入的地方多了python34的字样，实际上，此时系统做的事情就是把默认2.7环境从PATH中去除，再把3.4对应的命令加入PATH

# 此时，再次输入
python --version
# 可以得到`Python 3.4.5 :: Anaconda 4.1.1 (64-bit)`，即系统已经切换到了3.4的环境

# 如果想返回默认的python 2.7环境，运行
deactivate python34 # for Windows
source deactivate python34 # for Linux & Mac

# 删除一个已有的环境
conda remove --name python34 --all

#查看已经安装的环境,前被激活的环境会显示有一个星号或者括号。
conda info -envs
```


### Conda 包管理
Conda的包管理与`pip`功能类似。
```Bash
# 安装scipy
conda install scipy
# conda会从从远程搜索scipy的相关信息和依赖项目

# 查看当前环境下已安装的包
conda list

# 查看某个指定环境的已安装包
conda list -n python34

# 查找package信息
conda search numpy

# 安装package
conda install -n python34 numpy
# 如果不用-n指定环境名称，则被安装在当前活跃环境
# 也可以通过-c指定通过某个channel安装

# 更新package
conda update -n python34 numpy

# 删除package
conda remove -n python34 numpy
```

### Anaconda 在openSUSE中使用问题
在openSUSE中，如果使用当前用户安装Anaconda，在机器重启之后会出现输入密码无法进入界面（kde）的情况，弹出对话框`Could not start d-bus. can you call qdbus-qt5 `，点击确定就会卡死在那里。

这个问题在openSUSE官方论坛找到解决办法，[Thread: Could not start d-bus. can you call qdbus-qt5 ?](https://forums.opensuse.org/showthread.php/520970-Could-not-start-d-bus-can-you-call-qdbus-qt5)

现将解决办法摘出：
> i did install Anaconda before closing the last time

```Bash
> qtpaths --binaries-dir
/home/haib/anaconda3/bin

> which qtpaths
/home/haib/anaconda3/bin/qtpaths
```

This means that `startkde` tries to find `qdbus-qt5` in `/home/haib/anaconda3/bin`, where it doesn't exist.
You basically have these options to fix it:

+ remove the `PATH=` line that the Anaconda installer added from your `~/.bashrc` and set the path manually before running Anaconda
+ copy or symlink `qdbus-qt5` in `/home/haib/anaconda3/bin`
+ modify `/usr/bin/startkde` and add the explicit path to the call to `qtpaths`, i.e. replace `qtpaths` with `/usr/bin/qtpaths`

Btw, I submitted the second change to openSUSE's Plasma5 packages recently so this should not happen any more in Tumbleweed and Leap 42.2, but not yet as update for 42.1.
Though, I still think it's a bug in Anaconda's installer that might break other things as well.

采用上面任何一种方法都可以解决这个问题，我个人是通过今日`iceWM`界面对`~/.bashrc`进行修改，注释掉PATH
