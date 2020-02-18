---
title: 使用Live CD重置linux密码
date: 2018-08-07 14:58:00
tags: [Linux]
categories: [Linux]
---

参考：http://www.microhowto.info/howto/reset_a_forgotten_root_password_using_a_live_distribution.html

#### 前情

1. 使用live CD进入系统
2. 查看/dev下面有多少磁盘文件，然后使用mount挂载，挂载之后进入文件目录查看，是不是原系统的root分区
3. 明显的标志就是分区里面有`/etc`、 `/dev`目录，找到之后就继续操作

#### 挂载根分区：

```
mkdir /mnt/recover
mount /dev/sda2 /mnt/recover
```

#### 使用chroot进入根分区

The `chroot` command allows you to move the filesystem root to some subdirectory of the current root. In this case you want to move it to `/mnt/recover`:

```
chroot /mnt/recover
```

#### 更改原系统密码：

As the root user of the system to be recovered you should now be able to change the root password in the normal manner:
```
passwd
```

The passwords for other local accounts can be changed similarly:
```
passwd user
```

Because you are root, it should not be necessary to enter the previous password.

Note that passwords provided by a remote authentication protocol such as Kerberos or LDAP cannot be reset using this method.

#### 退出chroot:

You can exit from the `chroot` shell in the same way as any other shell, for example using the `exit` command:
```
exit
```

#### 取消挂载 

Unmount the root partition
```
umount /mnt/recover
```


### 另一种方法：直接修改password文件

Directly editing the password file

It is possible to achieve the same effect by directly editing the password file. This is significantly more risky than using the `passwd` command, but may prove useful if you can edit files but are unable to execute binaries.

The file you need to edit is `/etc/passwd`. Each line is a colon-separated list of fields, the first two of which are the username and password for an account. Here is a sample:

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/bin/sh
bin:x:2:2:bin:/bin:/bin/sh
sys:x:3:3:sys:/dev:/bin/sh
```

In each of these four entries of this example the password field is set to `x`, meaning that the encrypted password can be found in `/etc/shadow`. If you replace the `x` (or whatever else is in the second field) with the empty string then no password will be needed:

```
root::0:0:root:/root:/bin/bash
```

It would be prudent to make a backup of `/etc/passwd` before making any changes, because the mapping between usernames and UIDs would be very tedious to reconstruct if it were lost. You should also consider isolating the machine from any networks while it is without a root password, as it will obviously be very insecure during this period.

The `x` should be re-inserted before setting a new root password, otherwise it will be stored in `/etc/passwd` instead of `/etc/shadow`.



### 可能遇到的错误
#### 如果你更改了磁盘的名字，那它可能不是以/sda开头。那应该怎么办？

1. 查看物理卷：pvs
```
PV       VG       Fmt   Attr PSize PFree 
/dev/sda2   VolGroup00 lvm2 a- 279.22G 32.00M
```

2. 查看卷组：vgs
```
VG       #PV #LV #SN Attr VSize VFree 
VolGroup00 1 4 0 wz--n- 279.22G 32.00M
```

3. 查看逻辑卷：lvdisplay
```
--- Logical volume ---
   LV Name             /dev/VolGroup00/LogVol03
   VG Name             VolGroup00
   LV UUID             YhG8Fu-ZGPk-qt8D-AxgC-DzOU-dg1F-z71feI
   LV Write Access        read/write
  LV Status              unenable
   # open                 1
   LV Size             245.97 GB
   Current LE          7871
   Segments             1
   Allocation          inherit
   Read ahead sectors     auto
   - currently set to     256
   Block device           253:2
```

4. 如果未激活，需要激活逻辑卷：  
`vgchange -ay /dev/VolGroup00`

```
LV Status              available
```

5. 挂载逻辑卷：
`mount   /dev/VolGroup00/LogVol03   /home/lvm`


#### mount: unknown filesystem type 'LVM2_member'解决方案
解决办法：

需要安装 lvm2:  `yum install lvm2`