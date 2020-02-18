---
title: Vim 插入模式无法用backspace删除字符问题
date: 2017-01-09 11:44:03
tags: [Vim, Linux]
categories: [Linux]
---

解决这个问题需要两个步骤：

1. 去掉有关Vi的一致性模式，避免之前版本的Bug，在命令模式下： 

  `set nocompatible`

2. 设置backspace的工作方式： 

  `set backspace = indent,eol,start`




