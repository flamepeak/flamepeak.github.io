---
title: Rocket.Chat 学习与使用
date: 2018-08-09 15:23:41
tags: [RocketChat]
categories: [Web后端]
---

### 开发环境
1. 安装Git 
`yum install git`
2. 安装Meteor  
`curl https://install.meteor.com/ | sh`
3. clone and run
```
git clone https://github.com/RocketChat/Rocket.Chat.git
cd Rocket.Chat
meteor npm start
```

#### 可能遇到的错误
1. g++ not found
```
centos： 
yum -y update gcc 
yum -y install gcc+ gcc-c++

ubuntu： 
apt-get update gcc 
apt-get install g++
```

2. node-pre-gyp ERR! Completion callback never invoked!
这个错误原因未知，花了很长时间解决，最后用了一个简单的办法。
去npmjs网站查看[node-pre-gyp](https://www.npmjs.com/package/node-pre-gyp)的版本  

然后，修改Rocket.Chat目录下文件package-lock.json，查找文件目录里面的所有`node-pre-gyp`，把版本号换成最新的版本
