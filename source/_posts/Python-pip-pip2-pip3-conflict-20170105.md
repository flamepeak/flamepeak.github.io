---
title: pip pip2 pip3冲突与使用
date: 2017-01-05 09:49:39
tags: [Python]
categories: [Python]
---
在linux系统中，如果同时安装python2和python3，在使用pip时会出现问题。
查看pip对应版本，可以：
```
pip --version
pip 7.1.2 from /usr/lib/python3.4/site-packages (python 3.4)
```
那么，如果我们想指定安装，可以使用下面的方法：
```python
## 对于 Linux ##
sudo pip install sth
# 或者明确版本
sudo pip2 install sth
sudo pip3 install sth

## 对于 Windows ##

# 如果仅安装 python2
pip install sth

# 如果安装有 python3, 则需要明确 pip 版本
py -2 -m pip install sth
py -3 -m pip install sth
```
