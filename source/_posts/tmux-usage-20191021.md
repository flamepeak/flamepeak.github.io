---
title: tmux 使用
date: 2019-10-21 09:33:00
tags: [Linux, tmux]
categories: [Linux]
---

[参考](https://www.cnblogs.com/liuguanglin/p/9290345.html)

### tmux基本结构

tmux的结构包括会话(session)、窗口(window)、窗格(pane)三部分，会话实质是伪终端的集合，每个窗格表示一个伪终端，多个窗格展现在一个屏幕上，这一屏幕就叫窗口。

### 基本用法
tmux默认的快捷键前缀是Ctrl+b(下文用prefix指代)，按下前缀组合键后松开，再按下命令键进行快捷操作，比如使用prefix d分离会话（应该写作prefix d而不是prefix+d，因为d键不需要与prefix同时按下）。


### 快捷键

#### 会话管理　



```sh
tmux new　　创建默认名称的会话（在tmux命令模式使用new命令可实现同样的功能，其他命令同理，后文不再列出tmux终端命令）

tmux new -s mysession　　创建名为mysession的会话

tmux ls　　显示会话列表

tmux a　　连接上一个会话

tmux a -t mysession　　连接指定会话

tmux rename -t s1 s2　　重命名会话s1为s2

tmux kill-session　　关闭上次打开的会话

tmux kill-session -t s1　　关闭会话s1

tmux kill-session -a -t s1　　关闭除s1外的所有会话

tmux kill-server　　关闭所有会话
```


```sh
prefix s　　列出会话，可进行切换

prefix $　　重命名会话

prefix d　　分离当前会话

prefix D　　分离指定会话
```

#### 窗口管理

```sh
prefix c　　创建一个新窗口

prefix ,　　重命名当前窗口

prefix w　　列出所有窗口，可进行切换

prefix n　　进入下一个窗口

prefix p　　进入上一个窗口

prefix l　　进入之前操作的窗口

prefix 0~9　　选择编号0~9对应的窗口

prefix .　　修改当前窗口索引编号

prefix '　　切换至指定编号（可大于9）的窗口

prefix f　　根据显示的内容搜索窗格

prefix &　　关闭当前窗口
```

#### 窗格管理

```sh
prefix %　　垂直方向创建窗格

prefix "　　水平方向创建窗格

prefix Up|Down|Left|Right　　根据箭头方向切换窗格

prefix q　　显示窗格编号

prefix o　　顺时针切换窗格

prefix }　　与下一个窗格交换位置

prefix {　　与上一个窗格交换位置

prefix x　　关闭当前窗格

prefix space(空格键)　　重新排列当前窗口下的所有窗格

prefix !　　将当前窗格置于新窗口

prefix Ctrl+o　　逆时针旋转当前窗口的窗格

prefix t　　在当前窗格显示时间

prefix z　　放大当前窗格(再次按下将还原)

prefix i　　显示当前窗格信息
```
 

　　
#### 其他命令
```sh
tmux list-key　　列出所有绑定的键，等同于prefix ?
tmux list-command　　列出所有命令
```

翻页scroll的使用  
```
tmux中多窗口表示时，没有滚动条，可以使用下面的方法翻页查看信息

绑定的快捷开始键 默认　ctrl ＋ｂ + [
使用 PgUp PgDn 实现上下翻页
键 q 退出
```

