---
title: Python3 以及pip使用问题解决与技巧
date: 2018-01-25 16:08:12
tags: [Python]
categories: [Python]
---

### Python3 api-ms-win-crt-runtime-l1-1-0.dll 丢失

环境Windows 7, 安装Python3.6时遇到，api-ms-win-crt-runtime-l1-1-0.dll 丢失的问题，原因api-ms-win-crt-runtime是MFC的运行时环境的库，python在windows上编译也是用微软的visual studio C++编译的，底层也会用到微软提供的C++库和runtime库，安装Visual C++ Redistributable for Visual Studio 2015 组件即可解决此问题。

下载地址[:Visual C++ Redistributable for Visual Studio 2015](https://www.microsoft.com/zh-cn/download/details.aspx?id=48145&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

### Python3 创建虚拟环境

Python3.3以上的版本通过venv模块原生支持虚拟环境，可以代替Python之前的virtualenv。

官方文档介绍[Virtual Environments and Packages](https://docs.python.org/3/tutorial/venv.html)

`python -m venv DirName` 这样就创建了一个文件夹名为DirName的虚拟环境  
下面启动这个虚拟环境，

```
cd DirName
cd Scripts
activate (或者activate.bat)  #在linux上，运行 source DirName/bin/activate
python -V  #查看这个环境的版本
```
这样即可启动这个虚拟环境

但是python3中的venv不是完全版的virtualenv有很多功能没有继承，例如`--relocatable`构建可移植的环境,例如在离线环境中使用python

可以尝试如下方法
```
#online
pip download virtualenv

#online env1
python -m virtualenv myenv
cd myenv
source bin/activate
pip install Flask
pip freeze > requirements.txt

#online env2 
pip download -r requirements.txt


#copy to offline

#offline
python -m virtualenv myenv2
cd myenv2
source bin/activate
cd -
cd offline
pip install --no-index --find-links="/path/to/download/dir/" -r requirements.txt
pip list

```


### Pip的使用

1. 如果pip没有安装：  
`python -m ensurepip --default-pip`

2. 安装SomePackage  
`python -m pip install SomePackage`
或者  
`pip install SomePackage`

3. 安装指定版本的SomePackage  
```
python -m pip install SomePackage==1.0.4
python -m pip install "SomePackage>=1.0.4"
```

4. 升级SomePackage  
`python -m pip install --upgrade SomePackage`

5. 如果系统里有多个版本的Python，怎么安装SomePackage？ 
在Linux、Mac上    
```
python2   -m pip install SomePackage
python2.7 -m pip install SomePackage
python3   -m pip install SomePackage
python3.4 -m pip install SomePackage
```

在windows上：   
```
py -2   -m pip install SomePackage
py -2.7 -m pip install SomePackage
py -3   -m pip install SomePackage
py -3.4 -m pip install SomePackage
```

6. 列出已经安装的Package  
`pip list`

7. 查看SomePackage信息  
`pip show SomePackage`

8. 卸载某SomePackage  
`pip uninstall SomePackage`

9. 将当前安装的package以及版本号写入文件  
`pip freeze > requirements.txt`

10. 安装requirements.txt里的相应版本的package  
`pip install -r requirements.txt`

###