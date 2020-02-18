---
title: visual 2019 cannot convert argument from PCHAR to LPWSTR
date: 2019-06-28 09:59:21
tags: [C]
categories: [Cryptography]
---


参考StackOverflow的回答：

Go to the Properties for your Project and under Configuration Properties/General, change the Character Set to "Not Set". This way, the compiler will not assume that you want Unicode characters, which are selected by default:

