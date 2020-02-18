---
title: Linux su sudo 区别
date: 2019-12-12 08:24:03
tags: [Linux]
categories: [Linux]
---



 `su` 命令的主要作用是让你可以在已登录的会话中切换到另外一个用户。换句话说，这个工具可以让你在不登出当前用户的情况下登录为另外一个用户。

 通过`su`可以在用户之间切换，而超级权限用户root向普通或虚拟用户切换不需要密码，而普通用户切换到其它任何用户都需要密码验证。 

 还有一种方法可以切换到 root 用户：运行 `su -` 命令

 `su` 命令与 `su -` 命令之间有什么区别呢？前者在切换到 root 用户之后仍然保持旧的（或者说原始用户的）环境，而后者则是创建一个新的环境（由 root 用户 `~/.bashrc` 文件所设置的环境），相当于使用 root 用户正常登录（从登录屏幕登录）。



## sudo与su

 两个命令的最大区别是：`sudo` 命令需要输入当前用户的密码，`su` 命令需要输入 root 用户的密码。

### sudo

sudo是一种权限管理机制，依赖于`/etc/sudoers`，其定义了授权给哪个用户可以以管理员的身份能够执行什么样的管理命令；

格式：`sudo -u USERNAME COMMAND`

默认情况下，系统只有root用户可以执行sudo命令。需要root用户通过使用`visudo`命令编辑sudo的配置文件`/etc/sudoers`，才可以授权其他普通用户执行sudo命令。

Sudo configuration might allow a user to execute some command with another user privileges without knowing the password.
`sudo -l` The `-l` (list) option will list out the allowed (and forbidden) commands for the user on the current host.
通常来检查非root用户可以以高权限运行命令的程序。

### su


su为`switch user`，即切换用户的简写。

su是最简单的身份切换名，用su我们能够进行不论什么用户的切换，一般都是`su - username`，然后输入password就ok了，可是root用su切换到其它身份的时候是不须要输入password的。

格式为两种：

```bash
su -l USERNAME（-l为login，即登陆的简写）
su USERNAME
```

 如果不指定USERNAME（用户名），默认即为root，所以切换到root的身份的命令即为：`su -root`或`su -`，`su root` 或`su `。

`su USERNAME`，与`su - USERNAME`的不同之处如下：

- `su - USERNAME`切换用户后，同时切换到新用户的工作环境中。
- `su USERNAME`切换用户后，不改变原用户的工作目录，及其他环境变量目录。

**如何设置初始su密码？**

当我们输入su时会发现要输入密码，我们尝试输入自己当前用户的密码，发现failure。

但我们又不知道su密码是什么？其实su初始密码可以通过下面方式进行设置：

```bash
`sudo` `passwd`
```

设置完后再输入su命令并键入刚才设置的密码即可进入~ 





