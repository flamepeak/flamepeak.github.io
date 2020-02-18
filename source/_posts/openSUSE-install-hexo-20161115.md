---
title: openSUSE 安装Git、Node.js、Hexo、pandoc
date: 2016-11-15 16:54:26
tags: [openSUSE, Hexo]
categories: [Linux]
---

## 安装Git
`zypper install git`

---

## 安装Node.js
Node.js官方有很详细的帮助，特别是提供了openSUSE官方的链接[Download Node.js via openSUSE one-click](http://software.opensuse.org/download.html?project=devel%3Alanguages%3Anodejs&package=nodejs)
按照指引可以很方便的安装Node.js。
如果不可以，针对42.1版本可以root运行以下代码：
```
zypper addrepo http://download.opensuse.org/repositories/devel:languages:nodejs/openSUSE_Leap_42.1/devel:languages:nodejs.repo
zypper refresh
zypper install nodejs
```
可以通过`node -v`查看nodejs版本

针对42.2版本可以root运行以下代码：
```
zypper addrepo http://download.opensuse.org/repositories/devel:languages:nodejs/openSUSE_Leap_42.2/devel:languages:nodejs.repo
zypper refresh
zypper install nodejs
```

---

## 安装Hexo
`npm install -g hexo-cli`


如果以前有hexo工作目录，那么直接到目录`hexo g`,`hexo s`就可以
不能用`hexo init`


## 安装pandoc和插件
[A pandoc-markdown-flavor renderer for hexo](https://github.com/wzpan/hexo-renderer-pandoc)

1. 首先，安装[pandoc](http://pandoc.org);
```
sudo zypper install pandoc
```
2. 然后，`cd`进入hexo根目录，然后执行下面的命令：
```
npm install hexo-renderer-pandoc --save
```

**注意：**
我在设置过程中出现如下错误：
`error: pandoc: YAML header is not an object "source" (line 5, column 1)`
最后找到原因是：

> There must be something in your document that looks like a YAML metadata block, but isn't.

也就是说`---`格式出现的问题，最后发现在使用`---`分割段落时需要在其后留一空行。这样问题就解决了。
