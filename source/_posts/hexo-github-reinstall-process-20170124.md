---
title: Hexo 重新安装
date: 2017-01-24 11:01:03
tags: [Hexo]
categories: [Web前端]
---

重新安装时，没有备份。结果悲剧的事情发生了，原始数据又一次丢失，这次还好，只丢了3篇文章。教训啊！

在重新部署的时候，遇到很多奇怪的问题，大多数是git的，看来git还要系统学一下，问题奇奇怪怪，一会出现一会不出现，搞得我头大。回来写篇短文记录一下错误解决办法。

## 安装前提
检查是否安装了下面的程序：
+ [Node.js](http://nodejs.org/)
+ [Git](http://git-scm.com/)

没有安装的话，就需要安装，在openSUSE上：
```bash
zypper install nodejs
zypper install git
```
安装完成后，需要安装hexo：
```bash
$ npm install -g hexo-cli
```

```bash
npm install hexo --save
```

```bash
npm install hexo-deployer-git --save
npm install hexo-renderer-marked --save
npm install hexo-renderer-pandoc --save


如果出问题的话，可以尝试更换渲染工具为 hexo-renderer-pandoc：
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-pandoc --save
```

注意：如果要使用pandoc, 主机上要安装pandoc，官网: [Pandoc](http://www.pandoc.org/)


## 建站
```bash
hexo init
```

安装依赖包：
```bash
npm install
```

## Github建立username.github.io仓库
略

## 部署文件到github
可能要配置SSH， 略

安装一下hexo-deployer-git：
`npm install hexo-deployer-git --save`

然后：
```
git init
```

添加子模块：
```
git submodule add git@github.com:flamepeak/hexo-theme-ghost-casper.git themes/ghost-casper/

git submodule add git@github.com:flamepeak/jacman.git themes/jacman
```

source分支备份原始md文件
```bash
git checkout -b source
git add .
git commit -m  "FlamePeak"
git remote add origin git@github.com:flamepeak/flamepeak.github.io.git
git push origin source

```
