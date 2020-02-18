---
title: Hexo 多主机同步解决方法和配置
date: 2016-08-29 09:14:18
tags: Hexo
categories: [Web前端]
---

## Introduce
本文基于[关于博客同步的解决办法](http://devtian.me/2015/03/17/blog-sync-solution/),和[利用git解决hexo博客多PC间同步问题](http://chitanda.me/2015/06/18/hexo-sync-in-multiple-pc/),结合自己的实际操作，解决Hexo多PC同步问题。在此，对两位作者表示感谢！

## Scene
单位和家里两PC，同时都想更新blog。而由于hexo没有后台，而且全部文件都在本地生成，所以如果公司电脑上发表了A文章后回家又写了篇B文章，在家里上传后你会发现只有B文章而A文章没了（因为家里的PC上没有A文章的md文件），所以多台电脑同时用来写文章的时候，需要解决备份问题。
另外，笔者就遇到误删的情况，本地Hexo文件夹被彻底删除，原始md文件丢失，新md文件和以前html文件无法同时呈现，此问题至今未解决。

## Theory
hexo作为一个非常优秀的静态博客框架，hexo与传统的博客托管网站不同的一点是博客的源文件是保存在本地的，并通过hexo框架提供的hexo generate 和 hexo deploy命令将markdown文件生成相应的html文件，并发布到github-pages上去。

我们通过 hexo deploy发布到github-pages的时候，会将public目录（html文件）自动push到远程仓库的master分支。但这个对多终端博客同步没有任何意义，因为我们每次hexo generate都会根据source目录下的markdown源文件重新生成html文件，所以解决问题的关键是怎么同步source目录下的源文件，不仅如此，还有配置文件_config.yml，scanffolds目录，themes目录。

## Steps
首先我们进入到博客系统的根目录，比如blog目录，这里边有个.gitignore文件（如果该文件不存在，自己创建一个），里边默认已经把该忽略的目录文件都写好了，里边内容如下：

```bash
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/%
```

然后在blog目录初始化仓库，切换到source分支，关联远程仓库，并push到远程仓库的source分支
```bash
$ cd blog
$ git init                 #在当前目录新建一个Git代码库
$ git checkout -b source   #新建一个source分支，并切换到该分支
$ git add .      ##添加blog目录下所有文件(.gitignore声明的文件不包含在内)
$ git commit -m "first commit"    ##添加更新说明
$ git remote add origin git@github.com:username/username.github.io.git
$ git push origin source   #推送更新到云端服务器
```

### 添加本地文件到仓库并同步到git上，可以用：
```bash
git add .
git commit -m "first commit"
git push origin source
```

### 将git的内容同步到B主机
在另外一台电脑上，先把node环境配好，安装hexo。
```bash
npm install hexo-cli -g
```

注意不要再执行：
```bash
hexo init blog
```
取而代之的是
```bash
git clone -b source git@github.com:username/username.github.io.git
cd username.github.io
npm install //根据package.json来下载依赖包
```

（如果主题是自己的话，作为子模块存在）至此，我们已经把md文件下载完毕，但是themes目录是空的，现在需要：
```bash
git submodule init // 这句很重要
git submodule update
```

这样把远程仓库的source分支克隆下来，然后安装依赖包。接下来我们就可以继续写博客了
```bash
$ hexo new "about hexo sync"
$ hexo generate
$ hexo deploy
$ git add .
$ git commit -m "add blog"
$ git push origin source
```

这样就完成了多终端的博客同步。

再到A主机的时候，只需要
`git pull`
即可同步更新


## 下面说一下第三方主题的同步问题

我们一般会选择第三方主题的仓库直接git clone下来。这是一个非常不好的习惯，正确做法是：Fork该第三方主题仓库，这样就会在自己账号下生成一个同名的仓库，并对应一个url，我们应该git clone自己账号下的url。

这样做的原因是：我们很有可能在原来主题基础上做一些自定义的小改动，为了保持多终端的同步，我们需要将这些改动提交到远程仓库。而第三方仓库我们是无法直接push的。

这样就会出现git仓库的嵌套问题，我们通过git submodule来解决这个问题。
`git submodule add git@github.com:username/hexo-theme-next.git themes/next`
后面的theme 应该是自己fork到自己账户的theme

我们修改主题后:（可能不成功，不成功用下面的方法同步：关于git submodule push 不成功的问题）
```bash
git commit -am "refine UI"
git push origin source
```

在另外一个终端上执行：
```bash
git submodule init // 这句很重要
git submodule update
```
即可同步作为子模块的主题



## 关于config.xml的配置
```bash
deploy:
  type: git
  repository: git@github.com:username/username.github.io.git
  branch: master
```



## 关于git submodule push 不成功的问题
采用devtian 的submodule方法，可以成功建立，但是更新不成功。
在stackoverflow找到解决办法[Git submodule push](http://stackoverflow.com/questions/5814319/git-submodule-push)
A submodule is nothing but a clone of a git repo within another repo with some extra meta data (gitlink tree entry, .gitmodules file )
```Bash
$ cd your_submodule     #themes/ghost-casper  
$ git checkout master
$ git commit -a -m "commit in submodule"
$ git push
$ cd ..
$ git add your_submodule
$ git commit -m "Updated submodule"
```
原理我没有搞明白，至少可以实现，另外submodule更新较慢，需要多等一会,才能在github上看到
因为要备份md sourcefile，所以要记得多更新
```Bash
$ git add .
$ git commit -m "add blog"
$ git push origin source
```




## 添加RSS
`npm install hexo-generator-feed --save`
然后在博客配置文件_config.yml中添加启动
`plugins: hexo-generator-feed`




## hexo 代码高亮
格式是：
\`\`\`+语言名（比如java）
//代码内容
\`\`\`
example:
``` python
def func:
    print "Hello"
```



---

## 关于SSH key
新的主机或者是重装系统后的主机，在使用`git pull`时，会显示`Permission denied (publickey)`错误。这是因为系统里没有连接到github的SSH key。
下面介绍如何生成、使用SSH key，过程翻译自[Github Help](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/):

**生成新的SSH key**

1. 打开Git Bush
2. 粘贴下列代码到Git Bush，注意邮件地址要替换为你在Github的邮件地址。
    `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`
3. 当提示"Enter a file in which to save the key"时，按回车键。使用默认地址安装：
    `Enter a file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]`
4. 然后根据提示输入一个密码。获取更多信息,请参考[Working with SSH key passphrases](https://help.github.com/articles/working-with-ssh-key-passphrases):
    `Enter passphrase (empty for no passphrase): [Type a passphrase]`
     `Enter same passphrase again: [Type passphrase again]`

**添加你的SSH key到ssh-agent**

1. 确保ssh-agent启用：
    `eval "$(ssh-agent -s)"`
2. 添加SSH key到ssh-agent：
    `ssh-add ~/.ssh/id_rsa`
3. 将SSH key添加到GitHub账户。
   请参考[Adding a new SSH key to your GitHub account](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)

```bash
$ clip < ~/.ssh/id_rsa.pub
# Copies the contents of the id_rsa.pub file to your clipboard
```
