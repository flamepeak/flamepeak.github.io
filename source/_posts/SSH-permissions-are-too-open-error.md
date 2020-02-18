---
title: SSH permissions are too open error
date: 2020-01-22 15:45:16
tags: [Linux]
categories: [Linux]
---


Linux中遇到如下错误，

```
root@kali:~/Downloads# ssh -i prikeyfile  joanna@10.10.10.171
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0777 for 'prikey' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "prikey": bad permissions
joanna@10.10.10.171's password: 
Permission denied, please try again.
```

这个prikeyfile是自己保存的，权限设的高

遇到这个问题，需要把权限调低

```
# Keys need to be only readable by you:

chmod 400 ~/.ssh/id_rsa

# If Keys need to be read-writable by you:

chmod 600 ~/.ssh/id_rsa
```



