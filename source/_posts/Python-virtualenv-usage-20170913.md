---
title: Python virtualenv 使用与设置
date: 2017-09-13 08:20:52
tags: [Python]
categories: [Python]
---


## Installation

`pip install virtualenv`

***

## Usage

`virtualenv ENV`

Where `ENV` is a directory to place the new virtual environment. It has a number of usual effects :  

+ `ENV/lib/` and `ENV/include/` are created, containing supporting library files for a new virtualenv python. Packages installed in this environment will live under `ENV/lib/pythonX.X/site-packages/`.  
+ `ENV/bin` is created, where executables live - noticeably a new python. Thus running a script with `#! /path/to/ENV/bin/python` would run that script under this virtualenv’s python.  
+ The crucial packages pip and setuptools are installed, which allow other packages to be easily installed to the environment. This associated pip can be run from `ENV/bin/pip`.  

### activate script

For Windows systems, activation scripts are provided for the Command Prompt and Powershell.

```bash
cd ENV
cd Scripts
activate
```  

退出：
`deactivate`

```bash
For Linux:
cd ENV
source bin/activate
```  

退出：
`deactivate`


## 需求文件requirements.txt的创建及使用

python项目中可以包含一个 `requirements.txt` 文件，用于记录所有依赖包及其精确的版本号,以便新环境部署.

如果一个项目使用时virtualenv环境，还好办 `pip freeze` 就可以解决, 在虚拟环境中运行：  
`(venv) $ pip freeze >requirements.txt`

安装或升级包后，最好更新这个文件。

当需要创建这个虚拟环境的完全副本，可以创建一个新的虚拟环境，并在其上运行以下命令：  
`(venv) $ pip install -r requirements.txt`


***

## Linux Crontab and virtualenv

如果在python代码在一个virtualenv环境中，那应该怎么把这个脚本加到计划任务定时执行呢？

应该使用虚拟环境中的`python`，一般的路径在`virtual_env_dir/bin/python`:  

```Python
/home/my/virtual/bin/python /home/my/project/manage.py command arg
```

